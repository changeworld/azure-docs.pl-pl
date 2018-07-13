### <a name="troubleshoot-azure-diagnostics"></a>Rozwiązywanie problemów z funkcją Diagnostyka Azure

Jeśli zostanie wyświetlony następujący komunikat o błędzie, dostawca zasobów Microsoft.insights nie jest zarejestrowany:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Aby zarejestrować tego dostawcę zasobów, wykonaj następujące czynności w witrynie Azure Portal:

1.  W okienku nawigacji po lewej stronie kliknij pozycję *Subskrypcje*
2.  Wybierz subskrypcję określoną w komunikacie o błędzie
3.  Kliknij pozycję *Dostawcy zasobów*
4.  Odszukaj dostawcę *Microsoft.insights*
5.  Kliknij link *Zarejestruj*

![Rejestrowanie dostawcy zasobów microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Po zarejestrowaniu dostawcy zasobów *Microsoft.insights* spróbuj ponownie skonfigurować diagnostykę.


W programie PowerShell Jeśli zostanie wyświetlony następujący komunikat o błędzie, należy zaktualizować wersję programu PowerShell:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Zaktualizuj swoją wersję programu PowerShell do listopada 2016 r. (v2.3.0) lub nowszych wersji, zgodnie z instrukcjami przedstawionymi w [wprowadzenie do poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) artykułu.
