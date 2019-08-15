---
title: Przypisanie do partnera platformy Azure i użycie przez klienta | Portal Azure Marketplace
description: Przegląd sposobu śledzenia użycia klienta w rozwiązaniach w portalu Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: pabutler
ms.openlocfilehash: 98d33dd1aad726d4da8499006166dfd2c043378f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977151"
---
# <a name="azure-partner-customer-usage-attribution"></a>Udział partnera w zakresie użycia platformy Azure przez klienta

Jako partner oprogramowania dla platformy Azure, Twoje rozwiązania wymagają składników platformy Azure lub muszą być wdrażane bezpośrednio w infrastrukturze platformy Azure. Klienci, którzy wdrażają rozwiązanie partnerskie i udostępniają własne zasoby platformy Azure, mogą mieć trudności z uzyskaniem wglądu w stan wdrożenia i uzyskać wpływ na rozwój platformy Azure. Po dodaniu wyższego poziomu widoczności porównujesz się z zespołami sprzedaży firmy Microsoft i uzyskają środki na korzystanie z programów partnerskich firmy Microsoft.

Firma Microsoft oferuje teraz metodę ułatwiającą partnerom lepsze śledzenie użycia platformy Azure przez klientów na platformie Azure. Nowa metoda używa Azure Resource Manager do organizowania wdrożenia usług platformy Azure.

Jako partner firmy Microsoft możesz skojarzyć użycie platformy Azure z dowolnymi zasobami platformy Azure, które zostały wdrożone w imieniu klienta. Skojarzenie można utworzyć za pośrednictwem portalu Azure Marketplace, repozytorium szybkiego startu, prywatnych repozytoriów GitHub i jednej z nich. Przypisanie użycia klienta obsługuje trzy opcje wdrażania:

- Szablony Azure Resource Manager: Partnerzy mogą używać szablonów Menedżer zasobów, aby wdrożyć usługi platformy Azure w celu uruchomienia oprogramowania partnera. Partnerzy mogą utworzyć szablon Menedżer zasobów, aby zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure. Szablon Menedżer zasobów umożliwia klientom wdrażanie rozwiązania w całym cyklu życia. Możesz mieć pewność, że zasoby są wdrażane w spójnym stanie.
- Interfejsy API Azure Resource Manager: Partnerzy mogą wywoływać interfejsy API Menedżer zasobów bezpośrednio, aby wdrożyć szablon Menedżer zasobów lub wygenerować wywołania interfejsu API w celu bezpośredniego udostępnienia usług platformy Azure.
- Terraform Partnerzy mogą użyć programu Cloud Orchestrator, takiego jak Terraform, aby wdrożyć szablon Menedżer zasobów lub bezpośrednio wdrożyć usługi platformy Azure.

Przypisanie użycia klienta dotyczy nowego wdrożenia i nie obsługuje tagowania istniejących już wdrożonych zasobów.

W [aplikacji platformy Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)jest wymagane przypisanie użycia klienta: Oferta szablonu rozwiązania opublikowana w witrynie Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager
Wiele rozwiązań partnerskich jest wdrażanych w ramach subskrypcji klienta przy użyciu szablonów Menedżer zasobów. Jeśli masz szablon Menedżer zasobów, który jest dostępny w portalu Azure Marketplace, w witrynie GitHub lub w ramach przewodnika Szybki Start, proces modyfikowania szablonu w celu włączenia przypisywania użycia klienta powinien być prosty do przodu.

Aby uzyskać więcej informacji na temat tworzenia i publikowania szablonów rozwiązań, zobacz

* [Utwórz i Wdróż pierwszy szablon Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Oferta aplikacji platformy Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Wideo: [Tworzenie szablonów rozwiązań i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Dodawanie identyfikatora GUID do szablonu

Aby dodać unikatowy identyfikator globalny (GUID), należy dokonać pojedynczej modyfikacji pliku szablonu głównego:

1. [Utwórz identyfikator GUID](#create-guids) przy użyciu sugerowanej metody i [zarejestruj identyfikator GUID](#register-guids-and-offers).

1. Otwórz szablon Menedżer zasobów.

1. Dodaj nowy zasób do głównego pliku szablonu. Zasób musi znajdować się w pliku **mainTemplate. JSON** lub **azuredeploy. JSON** , a nie w żadnych zagnieżdżonych lub połączonych szablonach.

1. Wprowadź wartość identyfikatora GUID po prefiksie **identyfikatora PID** (np. PID-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Sprawdź, czy szablon nie ma błędów.

1. Opublikuj ponownie szablon w odpowiednich repozytoriach.

1. [Sprawdź sukces identyfikatora GUID we wdrożeniu szablonu](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Przykładowy kod szablonu Menedżer zasobów

Aby włączyć śledzenie zasobów dla szablonu, należy dodać do sekcji Resources następujący dodatkowy zasób. Pamiętaj o zmodyfikowaniu poniższego przykładowego kodu przy użyciu własnych danych wejściowych po dodaniu go do głównego pliku szablonu.
Zasób należy dodać tylko w pliku **mainTemplate. JSON** lub **azuredeploy. JSON** , a nie w żadnym z szablonów zagnieżdżonych lub połączonych.

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

## <a name="use-the-resource-manager-apis"></a>Korzystanie z Menedżer zasobów interfejsów API

W niektórych przypadkach warto wykonać wywołania bezpośrednio do Menedżer zasobów interfejsów API REST w celu wdrożenia usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) , aby umożliwić te wywołania. Można użyć jednego z zestawów SDK lub wywołać interfejsy API REST bezpośrednio w celu wdrożenia zasobów.

Jeśli używasz szablonu Menedżer zasobów, należy oznaczyć rozwiązanie, postępując zgodnie z instrukcjami opisanymi wcześniej. Jeśli nie korzystasz z szablonu Menedżer zasobów i tworzysz bezpośrednie wywołania interfejsu API, możesz oznaczyć wdrożenie, aby skojarzyć użycie zasobów platformy Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Dodawanie tagów do wdrożenia za pomocą interfejsów API Menedżer zasobów

Aby włączyć przypisanie użycia klienta, podczas projektowania wywołań interfejsu API Dołącz identyfikator GUID w nagłówku agenta użytkownika w żądaniu. Dodaj identyfikator GUID dla każdej oferty lub jednostki SKU. Sformatuj ciąg z prefiksem **identyfikatora PID** i Uwzględnij wygenerowany przez partnera identyfikator GUID. Oto przykład formatu identyfikatora GUID do wstawienia do agenta użytkownika:

![Przykładowy format identyfikatora GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Format ciągu jest ważny. Jeśli prefiks **identyfikatora PID** nie jest uwzględniony, nie jest możliwe wykonywanie zapytań dotyczących danych. Różne zestawy SDK śledzą się inaczej. Aby zaimplementować tę metodę, zapoznaj się z podejściem do pomocy technicznej i śledzenia dla preferowanego zestawu Azure SDK.

#### <a name="example-the-python-sdk"></a>Przykład: Zestaw SDK języka Python

Dla języka Python Użyj atrybutu **config** . Możesz dodać atrybut tylko do UserAgent. Oto przykład:

![Dodawanie atrybutu do agenta użytkownika](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Dodaj atrybut dla każdego klienta. Nie istnieje globalna Konfiguracja statyczna. Użytkownik może oznaczyć fabrykę klienta, aby upewnić się, że każdy klient śledzi śledzenie. Aby uzyskać więcej informacji, zobacz ten [przykład fabryki klienta w serwisie GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Oznacz wdrożenie przy użyciu Azure PowerShell

W przypadku wdrażania zasobów za pośrednictwem Azure PowerShell należy dołączyć identyfikator GUID przy użyciu następującej metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Dodawanie tagów do wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, aby dołączyć identyfikator GUID, Ustaw zmienną środowiskową **AZURE_HTTP_USER_AGENT** . Możesz ustawić tę zmienną w zakresie skryptu. Możesz również ustawić zmienną globalnie dla zakresu powłoki:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Aby uzyskać więcej informacji, zobacz [Azure SDK dla języka go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Użyj Terraform

Pomoc techniczna dla programu Terraform jest dostępna za pomocą wersji 1.21.0 dostawcy platformy [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)Azure:.  Ta pomoc techniczna dotyczy wszystkich partnerów, którzy wdrażają swoje rozwiązania za pośrednictwem usługi Terraform, oraz wszystkich zasobów wdrożonych i naliczanych przez dostawcę platformy Azure (w wersji 1.21.0 lub nowszej).

Dostawca platformy Azure dla Terraform dodał nowe pole opcjonalne o nazwie [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , które określa identyfikator GUID śledzenia, który jest używany dla danego rozwiązania. Wartość tego pola może być również źródłem ze zmiennej środowiskowej *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partnerzy, którzy chcą wdrożyć wdrożenie za pośrednictwem Terraform, muszą wykonać następujące czynności:

* Utwórz identyfikator GUID (identyfikator GUID należy dodać dla każdej oferty lub jednostki SKU)
* Zaktualizuj dostawcę platformy Azure, aby ustawić wartość *partner_id* na identyfikator GUID (nie należy wstępnie naprawić identyfikatora GUID przy użyciu identyfikatora "PID", po prostu ustaw go na rzeczywisty identyfikator GUID).

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

Identyfikator GUID jest unikatowym numerem odwołania, który ma 32 cyfr szesnastkowych. Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora GUID. Zespół usługi Azure Storage utworzył [formularz generatora GUID](https://aka.ms/StoragePartners) , który wyśle wiadomość E-mail na identyfikator GUID prawidłowego formatu i może być ponownie używany w różnych systemach śledzenia.

> [!Note]
> Zdecydowanie zaleca się użycie [formularza generatora GUID usługi Azure Storage](https://aka.ms/StoragePartners) w celu utworzenia identyfikatora GUID. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania](#faq).

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Można wybrać opcję użycia jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było podzielone.

Jeśli produkt zostanie wdrożony przy użyciu szablonu, który jest dostępny zarówno w portalu Azure Marketplace, jak i w witrynie GitHub, można utworzyć i zarejestrować 2 oddzielne identyfikatory GUID:

*   Produkt A w portalu Azure Marketplace
*   Produkt A w serwisie GitHub

Raportowanie jest wykonywane przez wartość partnera (identyfikator partnera firmy Microsoft) i identyfikatory GUID.

Identyfikatory GUID można także śledzić na bardziej szczegółowym poziomie, takim jak jednostka SKU, gdzie jednostki SKU są wariantem oferty.

## <a name="register-guids-and-offers"></a>Zarejestruj identyfikatory GUID i oferty

Identyfikatory GUID muszą być zarejestrowane w celu włączenia przypisywania do użycia przez klienta.

Wszystkie rejestracje dla identyfikatorów GUID szablonu są wykonywane za pośrednictwem witryny Azure Marketplace portal Cloud Partner (CPP).

Po dodaniu identyfikatora GUID do szablonu lub w agencie użytkownika i zarejestrowaniu identyfikatora GUID w CPP wszystkie wdrożenia są śledzone.

1. Zastosuj do [witryny Azure Marketplace](https://aka.ms/listonazuremarketplace) i uzyskaj dostęp do programu CPP.

   * Partnerzy muszą [mieć profil w programie CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Zachęcamy do korzystania z oferty w witrynie Azure Marketplace lub AppSource.
   * Partnerzy mogą rejestrować wiele identyfikatorów GUID.
   * Partnerzy mogą zarejestrować identyfikator GUID dla szablonów rozwiązań i ofert spoza witryny Marketplace.

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

1. W prawym górnym rogu wybierz ikonę konta, a następnie wybierz pozycję **profil wydawcy**.

   ![Wybierz profil wydawcy](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Na **stronie profil**wybierz pozycję **Dodaj identyfikator GUID śledzenia.**

   ![Wybierz pozycję Dodaj identyfikator GUID śledzenia](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. W polu **Identyfikator GUID śledzenia** wprowadź identyfikator GUID śledzenia. Wprowadź tylko identyfikator GUID bez prefiksu **PID** . W polu **niestandardowy opis** wprowadź nazwę lub opis oferty.

   ![Strona profilu](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Wprowadź identyfikator GUID i opis oferty](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Aby zarejestrować więcej niż jeden identyfikator GUID, ponownie wybierz pozycję **Dodaj identyfikator GUID śledzenia** . Na stronie są wyświetlane dodatkowe pola.

   ![Ponownie wybierz pozycję Dodaj identyfikator GUID śledzenia](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Wprowadź inny identyfikator GUID i opis oferty](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Wybierz pozycję **Zapisz**.

   ![Wybierz pozycję Zapisz](media/marketplace-publishers-guide/guid-dev-center-save.png)

Po dodaniu identyfikatora GUID do szablonu lub w agencie użytkownika i zarejestrowaniu identyfikatora GUID w CPP wszystkie wdrożenia są śledzone.

## <a name="verify-the-guid-deployment"></a>Weryfikowanie wdrożenia identyfikatora GUID

Po zmodyfikowaniu szablonu i uruchomieniu wdrożenia testowego należy użyć następującego skryptu programu PowerShell w celu pobrania wdrożonych i otagowanych zasobów.

Możesz użyć skryptu, aby sprawdzić, czy identyfikator GUID został pomyślnie dodany do szablonu Menedżer zasobów. Skrypt nie ma zastosowania do Menedżer zasobów wdrożeń API lub Terraform.

Zaloguj się do platformy Azure. Wybierz subskrypcję ze wdrożeniem, które chcesz zweryfikować przed uruchomieniem skryptu. Uruchom skrypt w kontekście subskrypcji wdrożenia.

**Identyfikator GUID** i nazwa obiektu **zasobów** wdrożenia są wymaganymi parametrami.

Możesz uzyskać [oryginalny skrypt](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) w serwisie GitHub.

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

Raport dotyczący przypisywania użycia klientów można znaleźć na pulpicie nawigacyjnym Centrum partnerskiego. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Aby wyświetlić raport, należy zalogować się przy użyciu poświadczeń Centrum partnerskiego. Jeśli wystąpią problemy z raportem lub zalogowaniem, Utwórz żądanie pomocy technicznej zgodnie z instrukcjami w sekcji Uzyskiwanie pomocy technicznej.

Wybierz pozycję śledzony szablon na liście rozwijanej Typ powiązania partnera, aby wyświetlić raport.

![Raport dotyczący przypisywania do użycia przez klienta](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Powiadamiaj klientów

Partnerzy powinni poinformować klientów o wdrożeniach korzystających z przypisywania użycia przez klienta. Firma Microsoft zgłasza do partnera użycie platformy Azure skojarzone z tymi wdrożeniami. Poniższe przykłady obejmują zawartość, której można użyć do powiadamiania klientów o tych wdrożeniach. W przykładach Zastąp \<> partnera nazwą swojej firmy. Partnerzy powinni upewnić się, że powiadomienia są wyrównane do zasad zachowania poufności danych i ich zbierania, w tym opcji dla klientów, które mają być wykluczone ze śledzenia.

### <a name="notification-for-resource-manager-template-deployments"></a>Powiadomienie dotyczące wdrożeń szablonów Menedżer zasobów

Po wdrożeniu tego szablonu firma Microsoft może zidentyfikować instalację \<partnera > oprogramowania przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które można znaleźć https://www.microsoft.com/trustcenter w witrynie.

### <a name="notification-for-sdk-or-api-deployments"></a>Powiadomienie o wdrożeniach zestawu SDK lub interfejsu API

Po wdrożeniu \<oprogramowania partnerskiego > Firma Microsoft może zidentyfikować \<instalację oprogramowania partnerskiego > przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które można znaleźć https://www.microsoft.com/trustcenter w witrynie.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Istnieją dwa kanały pomocy technicznej w zależności od występujących problemów.

W przypadku wystąpienia jakichkolwiek problemów w centrum partnerskim, takich jak wyświetlanie raportu dotyczącego użycia klienta lub logowanie, Utwórz żądanie pomocy technicznej z zespołem pomocy technicznej Centrum partnerskiego tutaj:[https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Jeśli potrzebujesz pomocy w ogólnym przypisywaniu do portalu Marketplace i/lub użyciu klienta, na przykład w sposobie konfigurowania przypisywania użycia klienta, wykonaj następujące czynności:

1. Przejdź do [strony pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=844975).

1. W obszarze **typ problemu**wybierz pozycję Dołączanie do **witryny Marketplace**.

1. Wybierz **kategorię** problemu:

   - W przypadku problemów z skojarzeniem użycia wybierz pozycję **inne**.
   - Aby uzyskać dostęp do problemów z programem CPP portalu Azure Marketplace, wybierz pozycję **problem z dostępem**.

     ![Wybierz kategorię problemu](media/marketplace-publishers-guide/lu-article-incident.png)

1. Wybierz pozycję **Uruchom żądanie**.

1. Na następnej stronie Wprowadź wymagane wartości. Wybierz **nadal**.

1. Na następnej stronie Wprowadź wymagane wartości.

   > [!Important]
   > W polu **tytuł zdarzenia** wprowadź wartość **Śledzenie użycia niezależnego dostawcy oprogramowania**. Opisz swój problem szczegółowo.

   ![Wprowadź śledzenie użycia niezależnego dostawcy oprogramowania dla tytułu zdarzenia](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Wypełnij formularz, a następnie wybierz pozycję **Prześlij**.

Możesz również otrzymywać wskazówki techniczne od doradcy technicznej partnera firmy Microsoft w celu uzyskania pomocy technicznej dotyczącej przedsprzedaży, wdrożenia i opracowywania aplikacji, aby zrozumieć i uwzględnić przypisanie użycia klientów.

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak przesłać żądanie konsultacji technicznych

1. Odwiedź [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney)stronę.
1. Wybierz pozycję Infrastruktura i zarządzanie chmurą, a nowa strona zostanie otwarta, aby wyświetlić drogę techniczną.
1. W obszarze usługi wdrażania kliknij przycisk Prześlij żądanie.
1. Zaloguj się przy użyciu konta MSA (MPN) lub usługi AAD (konto pulpitu nawigacyjnego partnera). na podstawie poświadczeń logowania zostanie otwarty formularz żądania online:
    * Ukończ/Przejrzyj informacje kontaktowe.
    * Szczegóły konsultacji mogą być wstępnie wypełnione lub wybrane z listy rozwijanej.
    * Wprowadź tytuł i opis problemu (Podaj jak najwięcej szczegółów).
1. Kliknij przycisk Prześlij

Wyświetl instrukcje krok po kroku dotyczące zrzutów ekranu pod adresem [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Co dalej

Skontaktujesz się z przedstawicielem firmy Microsoft dotyczącym partnerów technicznych w celu skonfigurowania połączenia w celu określenia zakresu Twoich potrzeb.

## <a name="faq"></a>Często zadawane pytania

**Jakie korzyści daje dodanie identyfikatora GUID do szablonu?**

Firma Microsoft udostępnia partnerom widok wdrożeń klientów rozwiązań i wgląd w ich użycie. Zarówno firma Microsoft, jak i partner mogą korzystać z tych informacji, aby zapewnić bliższe zaangażowanie między zespołami ds. sprzedaży. Zarówno firma Microsoft, jak i partner mogą korzystać z danych, aby uzyskać bardziej spójny widok wpływu na rozwój platformy Azure.

**Czy po dodaniu identyfikatora GUID można go zmienić?**

Tak, partner klienta lub implementacji może dostosować szablon i zmienić lub usunąć identyfikator GUID. Sugerujemy, aby partnerzy mogli aktywnie opisać rolę zasobu i identyfikator GUID klientom i partnerom, aby zapobiec usunięciu lub modyfikacji identyfikatora GUID. Zmiana identyfikatora GUID dotyczy tylko nowych, nie istniejących, wdrożeń i zasobów.

**Czy mogę śledzić szablony wdrożone z repozytorium innego niż Microsoft, takiego jak GitHub?**

Tak, o ile identyfikator GUID jest obecny podczas wdrażania szablonu, jest śledzone użycie. Partnerzy muszą mieć profil w programie CPP, aby zarejestrować identyfikatory GUID używane do wdrożenia poza portalem Azure Marketplace.

**Czy klient otrzymuje także raportowanie?**

Klienci mogą śledzić użycie poszczególnych zasobów lub grup zasobów zdefiniowanych przez klienta w ramach Azure Portal.

**Czy ta metodologia jest podobna do cyfrowej partnera rejestrowania (DPOR)?**

Ta nowa metoda łączenia wdrożenia i użycia z rozwiązaniem partnera zapewnia mechanizm łączenia rozwiązania partnerskiego z użyciem platformy Azure. DPOR jest przeznaczony do kojarzenia partnera konsultingowego (integratora systemów) lub zarządzania (dostawca usług zarządzanych) z subskrypcją platformy Azure klienta.

**Jakie korzyści niesie korzystanie z formularza generatora GUID usługi Azure Storage?**

Formularz generatora GUID usługi Azure Storage jest gwarantowany do wygenerowania identyfikatora GUID wymaganego formatu. Ponadto, jeśli używasz dowolnej metody śledzenia płaszczyzny danych usługi Azure Storage, możesz użyć tego samego identyfikatora GUID do śledzenia płaszczyzny kontroli witryny Marketplace. Dzięki temu można wykorzystać jednolity ujednolicony identyfikator GUID do przypisywania przez partnera bez konieczności obsługi oddzielnych identyfikatorów GUID.

**Czy można użyć prywatnego, niestandardowego wirtualnego dysku twardego dla oferty szablonu rozwiązania w portalu Azure Marketplace?**

Nie. Obraz maszyny wirtualnej musi pochodzić z portalu Azure Marketplace, patrz: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Możesz utworzyć ofertę maszyny wirtualnej w portalu Marketplace przy użyciu niestandardowego wirtualnego dysku twardego i oznaczyć ją jako prywatną, tak aby nikt nie mógł go zobaczyć. Następnie odwołuje się do tej maszyny wirtualnej w szablonie rozwiązania.

**Nie można zaktualizować właściwości *contentversion —* szablonu głównego?**

Prawdopodobnie błąd w niektórych przypadkach, gdy szablon jest wdrażany przy użyciu TemplateLink z innego szablonu, który oczekuje starszej Contentversion — z jakiegoś powodu. Obejście polega na użyciu właściwości Metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
