---
title: Usługa Azure autorstwa użycia partnerów i klientów
description: Omówienie sposobu śledzenia użycia przez klientów dla rozwiązań w portalu Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: ce862758d97737d16ef26ca7172cad39f8d8336a
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359981"
---
# <a name="azure-partner-customer-usage-attribution"></a>Uznanie autorstwa użycia klienta partnerów platformy Azure

Jako partner oprogramowania na platformie Azure rozwiązania albo wymagają składniki platformy Azure lub do wdrożenia bezpośrednio w infrastrukturze platformy Azure.  Już dziś gdy klient wdraża rozwiązanie partnerskie i udostępnia swoje zasoby platformy Azure, jest trudne dla partnerów uzyskać wgląd w stan tych wdrożeń i problemy z uzyskaniem optyką do wpływ na rozwój platformy Azure. Dodawanie wyższy poziom widoczności pomaga partnerów wyrównane z zespołami sprzedaży firmy Microsoft i Uzyskaj środki dla programów partnerskich firmy Microsoft.   

Firma Microsoft tworzy nową metodę w celu ułatwiania partnerom lepiej śledzić użycie platformy Azure, która wynika z klientem, wdrażania oprogramowania na platformie Azure. Ta nowa metoda opiera się na temat korzystania z usługi Azure Resource Manager do organizowania wdrożenia usług platformy Azure.

Jako partner firmy Microsoft można skojarzyć użycia platformy Azure z zasobami platformy Azure, którą możesz aprowizować w imieniu klienta.  To skojarzenie może odbywać się za pośrednictwem portalu Azure Marketplace, repozytorium Szybki Start, repozytoriów prywatnych usługi github i nawet 1 na 1 zaangażowania użytkowników.  Aby włączyć śledzenie, dostępne są dwie metody:

- Szablony usługi Azure Resource Manager: Szablony usługi Azure Resource Manager lub szablony rozwiązań do wdrożenia usług platformy Azure w celu uruchamiania oprogramowania partnera. Partnerzy, można utworzyć szablon usługi Azure Resource Manager, który definiuje infrastrukturę i konfigurację, które rozwiązania platformy Azure. Tworzenie szablonu usługi Azure Resource Manager umożliwia Tobie i Twoim klientom można wdrażać rozwiązania przez cały cykl życia, zapewniając spójny stan wdrożenia zasobów. 

- Interfejsy API Azure Resource Manager: partnerów można wywołać interfejsów API usługi Azure Resource Manager bezpośrednio z albo wdrażanie szablonu usługi Azure Resource Manager lub do generowania interfejsu API wywołuje bezpośrednio świadczenia usług platformy Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Metoda 1: Azure szablonów usługi Resource Manager 

Dzisiaj wielu rozwiązań partnerskich są wdrażane w ramach subskrypcji klienta przy użyciu szablonów usługi Azure Resource Manager.  Jeśli masz już szablonu usługi Azure Resource Manager dostępne w witrynie Azure Marketplace, w usłudze GitHub lub jako Szybki Start, proces modyfikowania szablon, aby włączyć tej nowej metody śledzenia powinna być bardzo proste.  Jeśli nie używasz obecnie szablonu usługi Azure Resource Manager, Oto kilka linków, aby umożliwić użytkownikom lepsze rozumienie szablony usługi Azure Resource Manager oraz instrukcje tworzenia takiego: 

*   [Tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Przewodnik, aby utworzyć szablon rozwiązań dla witryny Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Instrukcje: Dodawanie identyfikatora GUID do istniejącego szablonu usługi Azure Resource Manager

Dodawanie identyfikatora GUID jest pojedynczy modyfikacji pliku głównego szablonu:
 1. Utwórz identyfikator GUID, załóżmy, że wygenerowaną wartość jest eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Otwórz szablon usługi Azure Resource Manager
 3. Dodaj nowy zasób w pliku głównym szablonu. Zasób musi jedynie mieć w mainTemplate.json lub azuredeploy.json, nie w żadnym zagnieżdżone lub połączone szablony.
 4. Wprowadź identyfikator GUID po "identyfikator pid-", jak pokazano powyżej.

   Powinien on wyglądać podobnie jak w tym przykładzie: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Szablon sprawdzania pod kątem błędów
 6. Ponownie opublikowanie szablonu w odpowiednich repozytoriach

## <a name="sample-template-code"></a>Przykładowy kod szablonu

```

{ // add this resource to the mainTemplate.json (do not add the entire file)
      "apiVersion": "2018-02-01",
      "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your GUID here
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": []
        }
      }
    } // remove all comments from the file when done

```

## <a name="method-2-azure-resource-manager-apis"></a>Metoda 2: Interfejsy API Azure Resource Manager

W niektórych przypadkach warto wykonywać wywołania bezpośrednio w odniesieniu do interfejsów API REST usługi Azure Resource Manager do wdrażania usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) Aby włączyć tę opcję.  Możesz użyć jednego z zestawów SDK lub wywoływać interfejsy API REST bezpośrednio do wdrażania zasobów.

Jeśli używasz szablonu usługi Azure Resource Manager należy oznaczyć rozwiązania przy użyciu powyższych instrukcji.  Jeśli nie przy użyciu szablonu usługi Azure Resource Manager i wykonanie bezpośrednich wywołań interfejsu API możesz oznaczyć danego wdrożenia, aby skojarzyć użycia zasobów platformy Azure. 

**Jak oznaczyć wdrażania przy użyciu interfejsów API usługi Azure Resource Manager:** w tym podejściu podczas projektowania wywołania interfejsu API będzie zawierać identyfikator GUID w nagłówku agenta użytkownika w żądaniu. Identyfikator GUID powinny zostać dodane dla każdej oferty lub jednostki SKU.  Ciąg musi być sformatowany z prefiksem identyfikatora pid —, a następnie dołącz partnera wygenerowany identyfikator GUID.   

>[!Note] 
>Format identyfikatora GUID w celu wstawienia go do agenta użytkownika: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 / / enter Twojego identyfikatora GUID po "identyfikator pid-"

Format ciągu jest ważne. Jeśli nie jest prefiks "identyfikator pid-", nie można wysłać zapytanie dotyczące danych. Różne zestawy SDK to zrobić inaczej.  Aby zaimplementować tę metodę, należy przejrzeć pomocy technicznej i podejścia do preferowanego zestawu SDK usługi Azure. 

**Przykład korzystający z zestawu SDK języka Python:** dla języka Python, musisz użyć atrybutu "konfiguracji". Tylko można dodać agenta użytkownika. Oto przykład:

```python

client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
        client.config.add_user_agent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

>Musi to być wykonywane dla każdego klienta, brak globalnej konfiguracji statycznego (można zrobić fabryka klientów, należy upewnić się, że każdy klient jest już działa. 
>[Dodatkowe informacje](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Jak oznaczyć wdrażania przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure: w przypadku wdrożenia zasobów za pośrednictwem AzurePowerShell można dołączyć Twojego identyfikatora GUID za pomocą następującej metody:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Aby dołączyć Twojego identyfikatora GUID, korzystając z wiersza polecenia platformy Azure, należy ustawić zmienną środowiskową AZURE_HTTP_USER_AGENT.  Tę zmienną można ustawić w zakresie skryptu lub globalnie, ustawić do użytku zakresu powłoki:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Rejestrowanie identyfikatorów GUID/oferty

Aby identyfikator GUID, które mają zostać uwzględnione w naszej śledzenia muszą być zarejestrowane.  

Wszystkich rejestracji dla identyfikatorów GUID szablonu będzie odbywać się za pośrednictwem usługi Azure Marketplace Cloud Partner portalu (CPP). 

Dotyczą [portalu Azure Marketplace](http://aka.ms/listonazuremarketplace) dzisiaj i uzyskiwać dostęp do portalu dla partnerów w chmurze.

*   Partnerzy będą musieli [mieć profil w CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) i zachęca do listy oferty w portalu Azure Marketplace lub w usłudze AppSource 
*   Partnerzy będą mogli zarejestrować wiele identyfikatorów GUID 
*   Partnerzy również będą mogli zarejestrować identyfikator GUID dla rozwiązania spoza witryny Marketplace szablony/oferty

Po dodaniu identyfikator GUID szablonu lub agenta użytkownika i zarejestrowany identyfikator GUID w CPP będą śledzone wszystkie wdrożenia. 

## <a name="verification-of-guid-deployment"></a>Weryfikacja wdrożenia identyfikatora GUID 

Po modyfikacji szablonu i wykonać wdrożenia testowego służy poniższy skrypt programu PowerShell można pobrać zasoby wdrożone i oznaczony. 

Można użyć go, aby sprawdzić, w przypadku identyfikatora GUID została dodana do szablonu usługi Azure Resource Manager pomyślnie. Nie ma zastosowania do wdrażania interfejsu API usługi Azure Resource Manager.

Zaloguj się do platformy Azure i wybierz subskrypcję, która zawiera wdrożenia, które chcesz sprawdzić przed uruchomieniem skryptu. Muszą być uruchamiane w ramach subskrypcji wdrożenia.

Identyfikator GUID i grupie zasobów nazwę wdrożenia są wymagane parametry.

Można znaleźć oryginalnego skryptu [tutaj](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Wskazówki dotyczące tworzenia identyfikatorów GUID

Identyfikator GUID (unikatowy identyfikator globalny) jest unikatowy numer 32 cyfry szesnastkowej. Aby utworzyć identyfikator GUID, należy użyć GUID generator do utworzenia ich identyfikatorów GUID dla śledzenia.  Dostępnych jest wiele [online generatorów GUID](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) można użyć.

Zachęcamy do utworzenia Unikatowy identyfikator GUID dla każdego kanału oferty i dystrybucji.  Na przykład, jeśli masz dwa rozwiązania w zakresie, a oba są wdrażane za pomocą szablonu i są dostępne na rynku platformy Azure i w witrynie GitHub.  Utwórz cztery identyfikatorów GUID:

*   Oferty w portalu Azure Marketplace 
*   Oferty, A w witrynie GitHub
*   Oferta B w witrynie Azure Marketplace 
*   Oferta B w witrynie GitHub

Zgłoszenie będzie wykonywana przez partnera (identyfikator partnera firmy Microsoft) i identyfikator GUID. 

Możesz również śledzić identyfikatorów GUID na bardziej szczegółowym poziomie czyli jednostka SKU (których jednostek SKU są wariantów oferty).

## <a name="guidance-on-privacy-and-data-collection"></a>Wskazówki dotyczące ochrony prywatności i gromadzenia danych

Partnerzy powinien zapewniać komunikatów poinformowanie klientów, ich zezwalające wdrożeń obejmujących śledzenia identyfikator GUID Menedżera zasobów Azure firmy Microsoft do raportowania wykorzystania platformy Azure skojarzone z tym wdrożeniom na partnera.  Niektóre języka przykład znajduje się poniżej. W przypadku, gdy oznacza "PARTNER" należy wypełnić nazwę firmy. Ponadto partnerzy upewnij się, że język zgodnie z własnymi danymi zasady ochrony prywatności i kolekcji, włącznie z opcjami dla klientów, które mają być wykluczone z śledzenie: 

**Dla wdrożeń szablonu usługi Azure Resource Manager**

Podczas wdrażania tego szablonu, firma Microsoft będzie w stanie zidentyfikować instalację oprogramowania partnera z wdrożonych zasobów platformy Azure.  Microsoft będzie mieć możliwość skorelowania zasobów platformy Azure używane do obsługi oprogramowania.  Firma Microsoft zbiera te informacje, aby zapewnić subskrybentom w maksymalnym swoich produktów i wykorzystywać swoją działalność. Te dane będą zbierane i zarządzane przez zasady ochrony prywatności firmy Microsoft, które można znaleźć w folderze https://www.microsoft.com/trustcenter. 

**W przypadku wdrożeń zestawu SDK lub interfejsu API**

Podczas wdrażania oprogramowania partnera firmy Microsoft, będzie można zidentyfikować instalację oprogramowania partnera z zasobami platformy Azure wdrożone.  Microsoft będzie mieć możliwość skorelowania zasobów platformy Azure używane do obsługi oprogramowania.  Firma Microsoft zbiera te informacje, aby zapewnić subskrybentom w maksymalnym swoich produktów i wykorzystywać swoją działalność. Te dane będą zbierane i zarządzane przez zasady ochrony prywatności firmy Microsoft, które można znaleźć w folderze https://www.microsoft.com/trustcenter.

## <a name="support"></a>Pomoc techniczna

Aby uzyskać pomoc, wykonaj następujące czynności:
 1. Odwiedź stronę pomocy technicznej znajduje się w [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. W przypadku problemów z użycia skojarzenie — wybierz typ problemu: **przechodzenia do portalu Marketplace** i kategorii: **innych** a następnie kliknij przycisk **uruchom żądanie.** 
>[!Note]
>Problemy związane z dostępem do portalu Azure Marketplace Cloud Partner — wybierz typ problemu: **przechodzenia do portalu Marketplace** i kategorii: **Problem z dostępem** a następnie kliknij przycisk **uruchom żądanie.**
 3. Wypełnij wymagane pola na następnej stronie, a następnie kliknij przycisk **Kontynuuj.**
 4. Wypełnij pola dowolny tekst na następnej stronie.  
 

 
>[!Important] 
>Wprowadź tytuł zdarzenia z **"Śledzenia użycia niezależnego dostawcy oprogramowania"** i opisz swój problem szczegółowo w polu dużych dowolny tekst po.  Ukończenia pozostałej części formularza, a następnie kliknij przycisk **przesyłania**.

## <a name="faqs"></a>Często zadawane pytania

**Co to jest korzyść dodawania identyfikatora GUID do szablonu?**

Firma Microsoft zapewni partnerom widok wdrożeń klientów swoje szablony i szczegółowe informacje na ich użycia.  Microsoft i partnerów również użyć tych informacji do zwiększania zaangażowania bliżej między zespołami sprzedaży. Firma Microsoft i partnerów również służy do bardziej spójny widok partnera usługi wpływu na rozwój platformy Azure. 

**Identyfikator GUID, kto może dodawać do szablonu?**

Zasób śledzenia ma na celu łączenie rozwiązania partnera do klientów platformy Azure użycia.  Dane użycia jest powiązany z partnerem firmy Microsoft Partner Network tożsamości (identyfikator MPN), a następnie reporting będą dostępne dla partnerów w chmurze partnerem portalu (CPP).  

**Po dodaniu identyfikator GUID można je zmienić?**
 
Tak, klienta lub implementacji partnera może dostosować szablon i można zmienić lub usunąć identyfikator GUID. Zaleca się, że partnerzy aktywnie opisywania roli zasobów i identyfikator GUID dla ich klientów i partnerów, aby uniemożliwić usunięcie lub edycji do śledzenia identyfikator GUID.  Zmiana identyfikatora GUID wpłynie tylko na nowe, nie istniejących wdrożeń i zasobów.

**Kiedy reporting będą dostępne?**

Wersja beta, raportowania powinien być dostępny wkrótce.  Zgłoszenie zostanie zintegrowana w portalu partnerów chmury (CPP).

**Czy mogę śledzić szablony wdrażany z repozytorium firmy Microsoft, takich jak GitHub**

Tak, tak długo, jak identyfikator GUID jest obecny, podczas wdrażania szablonu, to użycie będą śledzone.  
Partnerzy musi mieć profil w portalu Cloud Partner, aby zarejestrować powiązanych szablonów opublikowane poza portalem Azure Marketplace. 

**Czy istnieje różnica w przypadku wdrażania szablonu z portalu Azure Marketplace i innych repozytoriów, takich jak GitHub?**

Tak, partnerzy, którzy publikowanie ofert w portalu Azure Marketplace może zostać wyświetlony bardziej szczegółowych danych we wdrożeniach w portalu Azure Marketplace.  Partnerzy będą mogli korzystać z udostępnianie ich oferta dla klientów w portalu Azure Marketplace i w portalu zarządzania systemu Azure. Oferty w witrynie Azure marketplace również generować potencjalnych klientów dla partnera.

**Co zrobić, jeśli utworzyć niestandardowy szablon dla poszczególnych zaangażowania?**

Nadal zachęcamy Dodaj identyfikator GUID do szablonu.  Jeśli używasz istniejący identyfikator GUID, który został zarejestrowany, zostanie uwzględniony w raportach.  Jeśli tworzysz nowy identyfikator GUID, należy zarejestrować nowy identyfikator GUID można pobrać on uwzględniony w śledzeniu.

**Klient jednocześnie, oraz raportowania?**

Klienci są obecnie w stanie śledzić ich użycie funkcji pojedyncze zasoby lub grupy zasobów zdefiniowanych przez klienta w portalu zarządzania systemu Azure.   

**Ta metoda śledzenia jest podobne do cyfrowego partnera z rekordu (partnera DPOR)?**

Tej nowej metody nawiązywania połączenia z wdrożenia i użycia rozwiązania partnerskiego jest zapewnienie mechanizmu połączyć rozwiązania partnerskiego do użycia platformy Azure.  Jako uprawniony partner CYFROWY jest przeznaczona do skojarzenia z konsultacji (Integrator systemów) lub partnera zarządzania (Managed Service Provider) z subskrypcją Azure klienta.   
