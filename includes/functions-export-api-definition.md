## <a name="export-an-api-definition"></a>Eksportowanie definicji interfejsu API
Masz OpenAPI definicji dla funkcji, z [utworzyć definicję OpenAPI dla funkcji](../articles/azure-functions/functions-openapi-definition.md). Następnym krokiem w procesie jest można wyeksportować definicji interfejsu API, dzięki czemu PowerApps i Flow firmy Microsoft może być używany w niestandardowego interfejsu API.

> [!IMPORTANT]
> Należy pamiętać, że użytkownik muszą być podpisane Azure z tymi samymi poświadczeniami, których można użyć dla Twojego rozwiązania PowerApps i Microsoft Flow dzierżawy. Dzięki temu Azure utworzyć niestandardowego interfejsu API i był dostępny dla obu PowerApps i Flow firmy Microsoft.

1. W [portalu Azure](https://portal.azure.com), kliknij swoją nazwę aplikacji funkcji (takich jak **funkcja Pokaz energii**) > **funkcji platformy** > **definicji interfejsu API** .

    ![Definicja interfejsu API](media/functions-export-api-definition/api-definition.png)

1. Kliknij przycisk **wyeksportować do rozwiązania PowerApps + przepływu**.

    ![Źródło definicji interfejsu API](media/functions-export-api-definition/export-api-1.png)

1. W okienku po prawej stronie należy użyć ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Tryb eksportu**|Wybierz **Express** do automatycznego generowania niestandardowego interfejsu API. Wybieranie **ręcznego** definicji interfejsu API eksportu, ale następnie musi zaimportować certyfikat do PowerApps i Microsoft Flow ręcznie. Aby uzyskać więcej informacji, zobacz [wyeksportować do rozwiązania PowerApps i Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Środowisko**|Wybierz środowisko, które mają być zapisywane niestandardowego interfejsu API. Aby uzyskać więcej informacji, zobacz [Przegląd środowisk (rozwiązanie PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) lub [Przegląd środowisk (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę, jak `Turbine Repair`.|
    |**Nazwa klucza interfejsu API**|Wprowadź nazwę wyświetlaną aplikacji i przepływ konstruktorów w Interfejsie użytkownika niestandardowego interfejsu API. Należy pamiętać, że przykładzie zawiera przydatne informacje.|
 
    ![Eksportowanie do usług PowerApps i Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Kliknij przycisk **OK**. Niestandardowy interfejs API jest teraz utworzony i dodać do określonego środowiska.