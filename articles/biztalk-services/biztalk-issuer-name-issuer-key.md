---
title: Nazwa wystawcy i klucz wydawcy w usłudze BizTalk Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać nazwy wystawcy i klucza wystawcy dla usługi Service Bus lub kontroli dostępu (ACS) w usłudze BizTalk Services. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 5eac98ec88b960956c9a0931673e67f530aef8da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542186"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Nazwa wystawcy i klucz wydawcy

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Usługa Azure BizTalk Services używa nazwy wystawcy usługi Service Bus oraz klucza wystawcy i nazwa wystawcy kontroli dostępu i klucz wystawcy. W szczególności:

| Zadanie | Która nazwa wystawcy i klucz wydawcy |
| --- | --- |
| Wdrażanie aplikacji w programie Visual Studio |Nazwa wystawcy kontroli dostępu i klucz wydawcy |
| Konfigurowanie portalu usługi Azure BizTalk Services |Nazwa wystawcy kontroli dostępu i klucz wydawcy |
| Tworzenie przekaźniki LOB za pomocą usługi adaptera BizTalk Services w programie Visual Studio |Nazwa wystawcy magistrali usług i klucz wydawcy |

Ten temat zawiera instrukcje pobierania nazwy wystawcy i klucza wystawcy. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nazwa wystawcy kontroli dostępu i klucz wydawcy
Nazwa wystawcy kontroli dostępu i klucz wydawcy są używane przez następujące elementy:

* Twoja aplikacja Usługa Azure BizTalk Services w programie Visual Studio: Aby pomyślnie wdrożyć aplikacji usługi BizTalk w programie Visual Studio na platformie Azure, wprowadź nazwę wystawcy kontroli dostępu i klucz wydawcy. 
* Portalu usługi Azure BizTalk Services: Podczas tworzenia usługi BizTalk i Otwieranie portalu usługi BizTalk Services, Twoja nazwa wystawcy kontroli dostępu i klucz wydawcy są automatycznie rejestrowane dla wdrożenia za pomocą tej samej wartości kontroli dostępu.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Pobierz nazwę wystawcy kontroli dostępu i klucz wydawcy

Aby używać usługi ACS do uwierzytelniania i uzyskać wartości nazwy wystawcy i klucza wystawcy, ogólne kroki obejmują:

1. Zainstaluj [poleceń cmdlet programu Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Dodaj konto platformy Azure: `Add-AzureAccount`
3. Zwraca nazwę subskrypcji: `get-azuresubscription`
4. Wybierz swoją subskrypcję: `select-azuresubscription <name of your subscription>` 
5. Utwórz nową przestrzeń nazw: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Przykład:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Po utworzeniu nowej przestrzeni nazw usługi ACS, (która może potrwać kilka minut), wartości wystawcy, nazwa i klucz wydawcy są wymienione w parametrach połączenia: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Podsumowując:  
Nazwa wystawcy = SharedSecretIssuer  
Klucz wystawcy = SharedSecretKey

Więcej na temat [New AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) polecenia cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nazwa wystawcy magistrali usług i klucz wydawcy
Nazwa wystawcy magistrali usług i klucz wydawcy są używane przez usługi adaptera BizTalk Services. W projekcie usługi BizTalk Services w programie Visual Studio umożliwia usługi adaptera BizTalk nawiązać połączenie z lokalnym systemem Line of Business (LOB). Nawiązać połączenie, Utwórz LOB przekazywania i wprowadź swoje dane systemu LOB. W ten sposób można także wprowadzić nazwę wystawcy magistrali usługi i klucz wydawcy.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Aby pobrać nazwę wystawcy magistrali usługi i klucz wydawcy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj **usługi Service Bus**i wybierz przestrzeń nazw. 
3. Otwórz **zasady dostępu współdzielonego** właściwości, wybierz zasady i wyświetlić **parametry połączenia** dla nazwy i wartości klucza.  

## <a name="next"></a>Następne kroki
Dodatkowe tematy usługi Azure BizTalk Services:

* [Instalowanie zestawu SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Samouczki: Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Zobacz też
* [Instrukcje: Konfigurowanie tożsamości usługi za pomocą usługi zarządzania usługi ACS](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Inicjowanie obsługi administracyjnej](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Wykres stanu aprowizacji](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Kopia zapasowa i przywracanie](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Ograniczanie przepustowości](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

