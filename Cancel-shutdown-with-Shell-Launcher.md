---
title: Shell Launcher でシャットダウンをキャンセルする方法について
date: 2020-08-11 12:00:00
categories:
- Shell Launcher
tags:
- シャットダウン
---
Shell Launcher でシャットダウンをキャンセルする方法についてご紹介します。  
<!-- more -->
<br>

***
Shell Launcher でカスタムシェルアプリケーションを実行している時に、シャットダウンの通知を受け取って、未保存のデータを保存してからシャットダウンしたい場合があると思います。 
    
例えば、Explorer.exe がシェルとして実行されている時は、実行中のウィンドウ アプリケーションで、以下の様に予め ShutdownBlockReasonCreate() を実行しておき、シャットダウン時に WM_QUERYENDSESSION のウィンドウメッセージを受け取って FALSE を返せば、ユーザーがシャットダウンをキャンセルするかどうかを選ぶことができます。  

```C
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch (message)
    {
    case WM_COMMAND:
        {
            int wmId = LOWORD(wParam);
            // 選択されたメニューの解析:
            switch (wmId)
            {
            case IDM_ABOUT:
                DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
                break;
            case IDM_EXIT:
                DestroyWindow(hWnd);
                break;
            default:
                return DefWindowProc(hWnd, message, wParam, lParam);
            }
        }
        break;
    case WM_PAINT:
        {
            PAINTSTRUCT ps;
            HDC hdc = BeginPaint(hWnd, &ps);
            // TODO: HDC を使用する描画コードをここに追加してください...
            EndPaint(hWnd, &ps);
        }
        break;
    case WM_CREATE:
        ShutdownBlockReasonCreate(hWnd, L"Don't do it!");
        break;
    case WM_DESTROY:
        ShutdownBlockReasonDestroy(hWnd);
        PostQuitMessage(0);
        break;
    case WM_QUERYENDSESSION:
        return FALSE;
        break;
    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}
```

![Closing1app-and-shuttingdown.png](https://jpiotblog.github.io/images/Cancel-shutdown-with-Shell-Launcher/Closing1app-and-shuttingdown.png)

誠に恐れ入りますが、**Shell Launcher V1 では、制限として、これと同じことができる方法はありません。**

[Shell Launcher](https://docs.microsoft.com/en-us/windows-hardware/customize/enterprise/shell-launcher)
> You cannot prevent the system from shutting down. For Shell Launcher V1 and V2, you cannot block the session ending by returning FALSE upon receiving the WM_QUERYENDSESSION message in a graphical application or returning FALSE in the handler routine that is added through the SetConsoleCtrlHandler function in a console application.  

上記ドキュメントでは Shell Launcher V2 でも方法がない旨の記載となっておりますが、現時点では、例外として、Shell Launcher V2 を用いた以下の手順であれば、同じことができることを確認していますので、その詳細をご紹介していきます。  

今回は、Windows 10 IoT Enterprise LTSC 2019 x64 を使用します。Shell Launcher V2 は Windows 10 バージョン 1809 以降でサポートされており、バージョン 1809 では KB4551853 をインストールすることでご利用いただけます。  

---
## 手順

(1) Shell Launcher の機能を有効にした Windows 10 IoT Enterprise LTSC 2019 で、以下の KB を適用します。

- [2020 年 5 月 12 日 - KB4551853 (OS ビルド 17763.1217)](https://support.microsoft.com/ja-jp/help/4551853/windows-10-update-kb4551853)

   インターネット接続がある場合は、上記を Windows Update で適用いただくだけで、以下の KB が適用されますが、インターネット接続がない場合には、上記を適用する前に、Microsoft Update カタログから適用ください。

   [https://www.catalog.update.microsoft.com/Search.aspx?q=KB4549947](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4549947)

(2) ShellLauncherBridgeWmiHelpers.ps1 を以下のサイトからコピーし、 Windows 10 IoT Enterprise LTSC 2019 の PC 上の任意のフォルダ (ここでは例として C:\test とします) に置きます。

- [Shell Launcher V2 Bridge WMI Sample scripts](https://docs.microsoft.com/en-us/samples/microsoft/windows-iotcore-samples/samplebridgewmiscripts/)

(3) PsTools.zip を以下からダウンロードし、(2) のフォルダに置き、解凍します。

- [PsExec v2.2](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)

(4) (UWP ではない) Windows フォームアプリとして、第二引数にメッセージ付きの ShutdownBlockReasonCreate() と SetProcessShutdownParameters() の第一引数に 0x500 を入れ、WM_QUERYENDSESSION は FALSE で返します。  
ここでは、例として以下のコードとしています。  
これをビルドしたモジュールを 2. のフォルダに置きます。  

```C
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch (message)
    {
    case WM_COMMAND:
        {
            int wmId = LOWORD(wParam);
            // 選択されたメニューの解析:
            switch (wmId)
            {
            case IDM_ABOUT:
                DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
                break;
            case IDM_EXIT:
                DestroyWindow(hWnd);
                break;
            default:
                return DefWindowProc(hWnd, message, wParam, lParam);
            }
        }
        break;
    case WM_PAINT:
        {
            PAINTSTRUCT ps;
            HDC hdc = BeginPaint(hWnd, &ps);
            // TODO: HDC を使用する描画コードをここに追加してください
            EndPaint(hWnd, &ps);
        }
        break;
    case WM_CREATE:
        SetProcessShutdownParameters(0x500, 0);
        ShutdownBlockReasonCreate(hWnd, L"Don't do it!");
        break;
    case WM_DESTROY:
        ShutdownBlockReasonDestroy(hWnd);
        PostQuitMessage(0);
        break;
    case WM_QUERYENDSESSION:
        return FALSE;
        break;
    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}
```

(5) ShellLauncher.xml の例として、以下の様に記載したものを (2) のフォルダに置きます。Shell= の後にカスタムシェルアプリケーションへのフルパスを記載しています。DefaultAction には、Action="DoNothing" としています。

```xml
<?xml version="1.0" encoding="utf-8"?> 
<ShellLauncherConfiguration xmlns="http://schemas.microsoft.com/ShellLauncher/2018/Configuration" 
xmlns:v2="http://schemas.microsoft.com/ShellLauncher/2019/Configuration"> 
  <Profiles> 
    <DefaultProfile> 
      <Shell Shell="C:\test\SetProcessShutdownParameters.exe"> 
        <DefaultAction Action="DoNothing"/> 
      </Shell> 
    </DefaultProfile> 
  </Profiles> 
  <Configs/> 
</ShellLauncherConfiguration>
```

(6) Windows 10 IoT Enterprise LTSC 2019 の PC 上で、管理者権限のコマンドプロンプトから、以下を実行します。これは、システムアカウントで powershell.exe を実行するために必須のため、省略しないようご注意ください。

`psexec.exe -i -s powershell.exe`

(7) (6) で起動した PowerShell 上で、cd コマンドで 2. のフォルダ (例 C:\test) に移動し、「`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope LocalMachine`」などで .ps1 ファイルを実行可能にしたら、以下のコマンドを入力します。

`. .\ShellLauncherBridgeWmiHelpers.ps1`
`Set-ShellLauncherBridgeWmi -FilePath .\ShellLauncher.xml`

(8) OS を再起動したら、カスタムシェルアプリケーションが起動してきますので、シャットダウンしますと以下の様に表示されます。Shell Launcher V2 では、カスタムシェルアプリケーションのウィンドウは、必ず全画面表示になりますので、ここでのシャットダウンは、タスクマネージャーの [ファイル]-[新しいタスクの実行] でコマンドプロンプトを起動し、shutdown /s /t 0 を実行しています。  

![Closing4apps-and-shuttingdown.png](https://jpiotblog.github.io/images/Cancel-shutdown-with-Shell-Launcher/Closing4apps-and-shuttingdown.png)

---
## 参考ドキュメント  
- [Use Shell Launcher to create a Windows 10 kiosk](https://docs.microsoft.com/en-us/windows/configuration/kiosk-shelllauncher)
- [ShutdownBlockReasonCreate function](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-shutdownblockreasoncreate)
- [WM_QUERYENDSESSION message](https://docs.microsoft.com/en-us/windows/win32/shutdown/wm-queryendsession)
- [SetProcessShutdownParameters function](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-setprocessshutdownparameters)  
<br>  

以上の内容がお役に立てば幸いです。
***
`変更履歴`  
`2020/08/11 created by Tsuda`  
