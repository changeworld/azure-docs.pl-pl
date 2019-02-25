## <a name="pausing-resetting-or-stopping-the-session"></a>Wstrzymanie, resetowanie lub zatrzymywania sesji

Aby zatrzymać sesję tymczasowo, możesz wywołać metodę Stop. Ten sposób przestanie wszelkie obserwatorów i systemów przetwarzania środowiska, nawet wtedy, gdy wywołujesz ProcessFrame(). Następnie należy wywołać Start(), aby wznowić przetwarzanie. Po wznowieniu dane środowisko, które już przechwytywane w sesji są obsługiwane.
