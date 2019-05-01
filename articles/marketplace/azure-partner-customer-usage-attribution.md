---
title: Usługa Azure autorstwa użycia partnerów i klientów
description: Omówienie sposobu śledzenia użycia przez klientów dla rozwiązań w portalu Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
manager: nunoc
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: 078815185ddb6018a394401f57f7557ac3aedb73
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123434"
---
# <a name="azure-partner-customer-usage-attribution"></a>Udział partnera w zakresie użycia platformy Azure przez klienta

Jako partner oprogramowania na platformie Azure rozwiązania wymagają składniki platformy Azure lub muszą zostać wdrożone bezpośrednio w infrastrukturze platformy Azure. Klienci, którzy wdrożyć rozwiązanie partnerskie i udostępniania własnych zasobów platformy Azure można trudno wgląd w stan wdrożenia i przedstawiane optyką negatywny wpływ na rozwój platformy Azure. Podczas dodawania wyższy poziom widoczności wyrównane z zespołami sprzedaży firmy Microsoft i Uzyskaj środki dla programów partnerskich firmy Microsoft.

Firma Microsoft oferuje teraz metodę w celu ułatwiania partnerom lepiej śledzić użycie platformy Azure wdrożeń klienta tego oprogramowania na platformie Azure. Nowa metoda używa usługi Azure Resource Manager do organizowania wdrożenia usług platformy Azure.

Jako partner firmy Microsoft można skojarzyć użycia platformy Azure z zasobami platformy Azure, które należy zarezerwować w imieniu klienta. Tworząc skojarzenie za pośrednictwem portalu Azure Marketplace, repozytorium Szybki Start, prywatne repozytoria GitHub i indywidualną zaangażowania użytkowników. Uznanie autorstwa użycia klienta obsługuje trzy opcje wdrażania:

- Szablony usługi Azure Resource Manager: Partnerzy mogą używać szablonów usługi Resource Manager do wdrażania usług platformy Azure w celu uruchamiania oprogramowania partnera. Partnerzy, można utworzyć szablon usługi Resource Manager w celu zdefiniowania infrastruktury i konfiguracji swoje rozwiązanie na platformie Azure. Szablon usługi Resource Manager umożliwia Tobie i Twoim klientom wdrażać rozwiązania przez cały cykl życia. Można mieć pewność, że Twoje zasoby są wdrażane w spójnym stanie.
- Interfejsy API Azure Resource Manager: Partnerzy mogą wywoływać interfejsy API usługi Resource Manager bezpośrednio, aby wdrożyć szablon usługi Resource Manager lub do generowania wywołań interfejsu API, bezpośrednie Inicjowanie obsługi administracyjnej usług platformy Azure.
- Terraform: Partnerzy mogą używać chmury orkiestratora, takiego jak narzędzie Terraform do wdrażania szablonu usługi Resource Manager lub bezpośrednio Wdrażaj usługi platformy Azure.

Uznanie autorstwa użycia klienta jest dla nowego wdrożenia i nie obsługuje tagowanie istniejących zasobów, które już zostały wdrożone.

Uznanie autorstwa użycia klienta jest wymagany na [aplikacji na platformie Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): Oferta szablonu rozwiązania opublikowane w portalu Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager
Wiele rozwiązań partnerskich są wdrażane w ramach subskrypcji klienta przy użyciu szablonów usługi Resource Manager. W przypadku szablonu usługi Resource Manager, który jest dostępny w portalu Azure Marketplace, w usłudze GitHub lub jako Szybki Start, proces, aby zmodyfikować szablon, aby włączyć autorstwa użycia klienta powinna być bardzo proste.

Aby uzyskać więcej informacji na temat tworzenia i publikowania szablony rozwiązań zobacz

* [Tworzenie i wdrażanie pierwszego szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Oferta platformy Azure aplikacja](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Wideo: [Tworzenie szablonów rozwiązań i zarządzanych aplikacji dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Dodaj identyfikator GUID szablonu

Aby dodać unikatowy identyfikator globalny (GUID), upewnij się jednym modyfikacji pliku głównego szablonu:

1. [Utwórz identyfikator GUID](#create-guids) przy użyciu metody sugerowane i [zarejestrować identyfikatora GUID](#register-guids-and-offers).

1. Otwórz szablon usługi Resource Manager.

1. Dodaj nowy zasób w pliku głównym szablonu. Zasób musi być zapisana w **mainTemplate.json** lub **azuredeploy.json** pliku tylko, a nie w żadnym zagnieżdżone lub połączone szablony.

1. Wprowadź wartość identyfikatora GUID po **pid -** prefiks (np. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Sprawdź szablon pod kątem błędów.

1. Ponownie opublikować szablon w odpowiednich repozytoriach.

1. [Sprawdzić, czy identyfikator GUID we wdrożeniu szablonu](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Kod szablonu Menedżera zasobów próbki

Aby włączyć śledzenie zasobów dla szablonu, należy dodać następujące dodatkowych zasobów w ramach sekcji zasobów. Upewnij się zmodyfikować poniżej przykładowego kodu z własnych danych wejściowych, po dodaniu do pliku głównego szablonu.
Zasób musi zostać dodane w **mainTemplate.json** lub **azuredeploy.json** pliku tylko, a nie w żadnym zagnieżdżone lub połączone szablony.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Korzystanie z interfejsów API usługi Resource Manager

W niektórych przypadkach warto wykonywać wywołania bezpośrednio w odniesieniu do interfejsów API REST usługi Resource Manager do wdrażania usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) Aby włączyć te wywołania. Możesz użyć jednego z zestawów SDK lub wywoływać interfejsy API REST bezpośrednio do wdrażania zasobów.

Jeśli używasz szablonu usługi Resource Manager należy oznaczać swoje rozwiązanie zgodnie ze wcześniejszym opisem. Jeśli nie są przy użyciu szablonu usługi Resource Manager i wykonanie bezpośrednich wywołań interfejsu API, możesz oznaczyć danego wdrożenia, aby skojarzyć użycia zasobów platformy Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tag wdrożenia za pomocą interfejsów API usługi Resource Manager

Aby umożliwić klientom, testując uznanie autorstwa podczas projektowania wywołania interfejsu API, obejmują identyfikator GUID w nagłówku agenta użytkownika w żądaniu. Dodaj identyfikator GUID dla każdej oferty lub jednostki SKU. Ciąg przy użyciu formatu **pid -** prefiksu i zawiera identyfikator GUID generowany przez partnera. Oto przykład format identyfikatora GUID w celu wstawienia go do agenta użytkownika:

![Przykładowy format identyfikatora GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Format ciągu jest ważne. Jeśli **pid -** prefiks nie jest uwzględniona, ponieważ nie ma możliwość wykonywania zapytań o dane. Różne zestawy SDK śledzić inaczej. Aby zaimplementować tę metodę, należy przejrzeć pomocy technicznej i śledzenie podejście do preferowanego zestawu SDK usługi Azure.

#### <a name="example-the-python-sdk"></a>Przykład: Python SDK

W przypadku języka Python, użyj **config** atrybutu. Ten atrybut można dodać tylko do agenta użytkownika. Oto przykład:

![Dodaj atrybut do agenta użytkownika](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Dodaj atrybut dla każdego klienta. Brak globalnej konfiguracji statycznej. Być może oznaczać fabryka klientów, należy upewnić się, że każdy klient służy do śledzenia. Aby uzyskać więcej informacji, zobacz ten [przykład fabryki klienta w witrynie GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Tag wdrożenia przy użyciu programu Azure PowerShell

W przypadku wdrożenia zasobów za pomocą programu Azure PowerShell, Dołącz swoje GUID za pomocą następującej metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Tag wdrożenia przy użyciu wiersza polecenia platformy Azure

Korzystając z wiersza polecenia platformy Azure do dołączenia z identyfikatorem GUID, ustaw **AZURE_HTTP_USER_AGENT** zmiennej środowiskowej. Tę zmienną można ustawić w zakresie skryptu. Można również ustawić zmienną globalnie dla zakresu powłoki:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Aby uzyskać więcej informacji, zobacz [zestawu Azure SDK dla języka Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Za pomocą narzędzia Terraform

Pomoc techniczna dotycząca narzędzia Terraform jest dostępna za pośrednictwem dostawcy usługi Azure 1.21.0 wydania: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Ta obsługa dotyczy wszystkich partnerów, którzy wdrażają swoje rozwiązanie za pomocą narzędzia Terraform i wszystkie zasoby wdrożone i opłaty naliczane przez dostawcę usługi Azure (wersja 1.21.0 lub nowszej).

Dostawca usługi Azure dla programu Terraform dodane nowe pole opcjonalne, o nazwie [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) czyli, gdzie Określ śledzenia GUID używanej dla Twojego rozwiązania. Wartość tego pola może również być pochodzących z *ARM_PARTNER_ID* zmiennych środowiskowych.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partnerzy, którzy chcą uzyskać ich wdrażanie za pomocą programu Terraform śledzone przez autorstwa użycia klienta, należy wykonać następujące czynności:

* Utwórz identyfikator GUID (identyfikator GUID powinien być dodane dla każdej oferty lub jednostki SKU)
* Aktualizowanie ich dostawcy usługi Azure, aby ustawić wartość *partner_id* zgodnie z identyfikatorem GUID (nie wstępne naprawa pamięci z identyfikatora GUID "identyfikator pid-", po prostu ustaw ją na rzeczywisty identyfikator GUID)

## <a name="create-guids"></a>Utwórz GUID

Identyfikator GUID jest unikatowy numer, zawierającej 32 cyfry szesnastkowe. Aby utworzyć identyfikatorów GUID dla śledzenia, należy użyć GUID generator. Zespół usługi Azure Storage został utworzony [formie generator GUID](https://aka.ms/StoragePartners) , otrzymasz wiadomość e-mail identyfikatora GUID w poprawnym formacie i może być ponownie używane w systemach różnych śledzenia.

> [!Note]
> Zdecydowanie zaleca się, że używasz [formie generator GUID usługi Azure Storage](https://aka.ms/StoragePartners) do utworzenia z identyfikatorem GUID. Aby uzyskać więcej informacji, zobacz nasze [— często zadawane pytania](#faq).

Firma Microsoft zaleca się utworzenie Unikatowy identyfikator GUID dla każdego kanału oferty i dystrybucji dla każdego produktu. Możesz zdecydować się na użycie jednego identyfikatora GUID wielu kanałach dystrybucji produktu, jeśli nie chcesz, raportowanie do podzielenia.

Jeśli wdrożenie produktu za pomocą szablonu i jest dostępna zarówno portalu Azure Marketplace i w witrynie GitHub, można tworzyć i zarejestruj 2 unikatowych identyfikatorów GUID:

*   Produkt w witrynie Azure Marketplace
*   Produkt A w witrynie GitHub

Raportowanie będzie odbywać się przez wartość partnera (identyfikator partnera firmy Microsoft) i identyfikatory GUID.

Można także śledzić identyfikatorów GUID na bardziej szczegółowym poziomie, takie jak SKU, których jednostek SKU są wariantów oferty.

## <a name="register-guids-and-offers"></a>Rejestrowanie identyfikatorów GUID i oferty

Identyfikatory GUID musi być zarejestrowany umożliwiające użycie klienta: uznanie autorstwa.

Wszystkie rejestracje dla identyfikatorów GUID szablonu są wykonywane za pośrednictwem usługi Azure Marketplace Cloud Partner portalu (CPP).

Po Dodaj identyfikator GUID szablonu lub agenta użytkownika, a następnie zarejestrować identyfikatora GUID w CPP, wszystkie wdrożenia są śledzone.

1. Dotyczą [portalu Azure Marketplace](https://aka.ms/listonazuremarketplace) i Uzyskaj dostęp do CPP.

   * Partnerzy są wymagane do [mieć profil w CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Możesz zachęcać do tworzenia listy oferty w portalu Azure Marketplace lub w usłudze AppSource.
   * Partnerzy mogą zarejestrować wiele identyfikatorów GUID.
   * Partnerzy mogą zarejestrować identyfikator GUID dla szablonów spoza witryny Marketplace rozwiązań i ofert.

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

1. W prawym górnym rogu wybierz ikonę Twojego konta, a następnie wybierz **profilem wydawcy**.

   ![Wybierz profil wydawcy](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Na **stronę profilu**, wybierz opcję **Dodawanie GUID śledzenia.**

   ![Wybierz opcję Dodaj identyfikator GUID śledzenia](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. W **śledzenia GUID** śledzenia wprowadź identyfikator GUID. Wprowadź tylko identyfikator GUID bez **pid -** prefiks. W **Opis niestandardowego** wprowadź swoje oferty nazwy lub opisu.

   ![Strona profilu](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Wprowadź identyfikator GUID i opis oferty](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Aby zarejestrować więcej niż jeden GUID, zaznacz opcję **Dodawanie GUID śledzenia** ponownie. Dodatkowe pola są wyświetlane na stronie.

   ![Ponownie wybierz pozycję Dodaj identyfikator GUID śledzenia](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Wprowadź inny identyfikator GUID i opis oferty](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Wybierz pozycję **Zapisz**.

   ![Wybierz opcję Zapisz](media/marketplace-publishers-guide/guid-dev-center-save.png)

Po Dodaj identyfikator GUID szablonu lub agenta użytkownika, a następnie zarejestrować identyfikatora GUID w CPP, wszystkie wdrożenia są śledzone.

## <a name="verify-the-guid-deployment"></a>Weryfikacja wdrożenia identyfikatora GUID

Po zmodyfikowaniu szablonu i uruchamiania wdrożenia testowego, należy użyć poniższy skrypt programu PowerShell można pobrać zasoby, które są wdrażane i oznaczony.

Możesz użyć skryptu, aby sprawdzić, czy identyfikator GUID został pomyślnie dodany do szablonu usługi Resource Manager. Skrypt nie ma zastosowania do wdrażania interfejsu API usługi Resource Manager.

Zaloguj się do platformy Azure. Wybierz subskrypcję do wdrożenia, który chcesz zweryfikować przed uruchomieniem skryptu. Uruchom skrypt wdrożenia, w ramach subskrypcji.

**GUID** i **resourceGroup** Nazwa wdrożenia są wymagane parametry.

Możesz uzyskać [oryginalnego skryptu](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) w witrynie GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Raport

Raport jest dostępny dla klienta użycie: uznanie autorstwa na pulpicie nawigacyjnym analizy Centrum partnerskiego. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)).

Wybierz szablon śledzone, na liście rozwijanej Typ skojarzenia partnera, aby wyświetlić raport.

![Raport dla klienta użycie: uznanie autorstwa](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Powiadom klientów

Partnerzy powinien poinformować swoich klientów dotyczące wdrożenia, które używają autorstwa użycia klientów. Firmy Microsoft raporty użycia platformy Azure, skojarzony z tych wdrożeń do partnera. Poniższe przykłady obejmują zawartość, która służy do powiadamiania klientów o tych wdrożeń. W przykładach należy zastąpić \<partnera > z nazwą Twojej firmy. Partnerzy upewnij się, że powiadomienie jest wyrównywany z danymi zasad ochrony prywatności i kolekcji, w tym opcje dla klientów, które mają być wykluczone ze śledzenia.

### <a name="notification-for-resource-manager-template-deployments"></a>Powiadomienie dla wdrożeń szablonu usługi Resource Manager

Podczas wdrażania tego szablonu, firma Microsoft jest możliwość określenia instalacji \<partnera > oprogramowanie z zasobami platformy Azure, które są wdrażane. Firma Microsoft jest możliwość skorelowania zasobów platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić subskrybentom w maksymalnym swoich produktów i wykorzystywać swoją działalność. Dane są zbierane i zarządzane przez zasady ochrony prywatności firmy Microsoft, które można znaleźć w folderze https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Powiadomienie dla wdrożenia zestawu SDK lub interfejsu API

Podczas wdrażania \<partnera > oprogramowania firmy Microsoft jest w stanie zidentyfikować instalacji \<partnera > oprogramowanie z zasobami platformy Azure, które są wdrażane. Firma Microsoft jest możliwość skorelowania zasobów platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić subskrybentom w maksymalnym swoich produktów i wykorzystywać swoją działalność. Dane są zbierane i zarządzane przez zasady ochrony prywatności firmy Microsoft, które można znaleźć w folderze https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli potrzebujesz pomocy dla dołączania do witryny Marketplace i/lub autorstwa użycia klienta, wykonaj następujące kroki.

1. Przejdź do [stronę pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=844975).

1. W obszarze **typ problemu**, wybierz opcję **przechodzenia do portalu Marketplace**.

1. Wybierz **kategorii** problemu:

   - W przypadku użycia skojarzenia problemów wybierz **innych**.
   - W przypadku problemów z dostępem za pomocą Azure Marketplace CPP wybrać **Problem z dostępem**.

     ![Wybierz kategorię problemu](media/marketplace-publishers-guide/lu-article-incident.png)

1. Wybierz **żądanie uruchomienia**.

1. Na następnej stronie wprowadź wymagane wartości. Wybierz przycisk **Kontynuuj**.

1. Na następnej stronie wprowadź wymagane wartości.

   > [!Important]
   > W **tytuł zdarzenia** wprowadź **śledzenia użycia niezależnego dostawcy oprogramowania**. Opisz swój problem szczegółowo.

   ![Wprowadź śledzenia użycia niezależnego dostawcy oprogramowania tytuł zdarzenia](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Wypełnij formularz, a następnie wybierz **przesyłania**.

Wskazówki techniczne może również odbierać od firmy Microsoft Partner Konsultant przedsprzedażna pomoc, wdrażania i scenariuszy tworzenia aplikacji do zrozumienia i dołączyć autorstwa użycia klienta.

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak przesłać żądanie konsultacji Technical Preview

1. Odwiedź stronę [ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney).
1. Wybierz infrastruktury chmury i zarządzania oraz Nowa strona zostanie otwarty w celu wyświetlania podróż Technical Preview.
1. W ramach wdrożenia usługi kliknij przycisk Prześlij przycisk żądania
1. Zaloguj się przy użyciu konta Microsoft (konta programu MPN) lub usługi AAD (pulpit nawigacyjny partnera konta); oparte na poświadczenia logowania, zostanie otwarty formularz zgłoszenia online:
    * Ukończenie/Przegląd informacji kontaktowych.
    * Szczegóły konsultacji wstępnie wypełnione, lub wybierz z listy rozwijane.
    * Wprowadź tytuł i opis problemu (Podaj jak najdokładniejsze informacje, jak to możliwe).
1. Kliknij przycisk Prześlij

Wyświetl instrukcje krok po kroku ze zrzutami ekranu na [ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Jaka jest przyszłość

Skontaktuje się z Tobą przez Microsoft Partner Konsultant skonfigurować wywołanie do zakresu Twoich potrzeb.

## <a name="faq"></a>Często zadawane pytania

**Co to jest korzyść dodawania identyfikatora GUID do szablonu?**

Firma Microsoft zapewnia partnerom widok wdrożeń klientów w ich rozwiązań i szczegółowe informacje na ich użycia. Firma Microsoft i partnerów można użyć tych informacji do zwiększania zaangażowania bliżej między zespołami sprzedaży. Microsoft i partnerów można użyć danych, aby uzyskać bardziej spójny widok partnera usługi wpływ na rozwój platformy Azure.

**Po dodaniu identyfikator GUID można go zmienić?**

Tak, klienta lub implementacji partnera może dostosować szablon i zmienić lub usunąć identyfikator GUID. Zaleca się, że partnerzy aktywnie opisywania roli zasobu i identyfikator GUID dla ich klientów i partnerów, aby uniemożliwić usunięcie lub edycji do identyfikatora GUID. Zmiana identyfikatora GUID dotyczy tylko nowych, nie istniejących wdrożeń i zasobów.

**Czy mogę śledzić szablony wdrażany z repozytorium firmy Microsoft, takich jak GitHub**

Tak, tak długo, jak identyfikator GUID jest obecny, podczas wdrażania szablonu, użycie jest śledzone oddzielnie. Partnerzy muszą mieć profil w CPP, aby zarejestrować identyfikatorów GUID używany do wdrożenia poza portalem Azure Marketplace.

**Klient jednocześnie, oraz raportowania?**

Klienci mogą śledzić ich użycie funkcji pojedyncze zasoby lub grupy zasobów przez klienta w witrynie Azure portal.

**Ta metoda jest podobne do cyfrowego partnera z rekordu (partnera DPOR)?**

Ta nowa metoda nawiązywania rozwiązania partnerskiego wdrożenia i użycia udostępnia mechanizm połączyć rozwiązania partnerskiego do użycia platformy Azure. Jako uprawniony partner CYFROWY jest przeznaczona do skojarzenia z konsultacji (Integrator systemów) lub partnera zarządzania (Managed Service Provider) z subskrypcją Azure klienta.

**Co to jest korzyść za pomocą formularza GUID Generator usługi Azure Storage?**

Formularz GUID Generator usługi Azure Storage jest gwarantowane do generowania identyfikatora GUID z wymaganym formatem. Ponadto jeśli używasz dowolnej metody śledzenia płaszczyzny danych usługi Azure Storage, możesz korzystać z tego samego identyfikatora GUID dla portalu Marketplace płaszczyznę kontroli śledzenia. Dzięki temu można korzystać z trasą jednolitego identyfikatora GUID dla partnerów: uznanie autorstwa bez konieczności obsługi oddzielnych identyfikatorów GUID.

**Czy można używać prywatnych, niestandardowego dysku VHD, skorzystania z oferty szablonu rozwiązania w witrynie Azure Marketplace?**

Nie, nie jest możliwe. Obraz maszyny wirtualnej musi pochodzić z portalu Azure Marketplace, zobacz: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Można utworzyć oferty maszyny Wirtualnej w portalu marketplace przy użyciu niestandardowego dysku VHD i oznacz ją jako prywatny, tak aby nie mogli je zobaczyć. Następnie odwołanie do tej maszyny Wirtualnej w szablonie rozwiązania.

**Nie można zaktualizować *contentversion —* właściwości głównego szablonu?**

Prawdopodobnie usterkę w niektórych przypadkach podczas wdrażania szablonu przy użyciu TemplateLink z innego szablonu, które oczekują starszych contentversion — przyczyny. Obejście polega na użyciu właściwość metadanych:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
