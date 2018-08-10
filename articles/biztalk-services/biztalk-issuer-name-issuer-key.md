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
ms.openlocfilehash: 78796b5dc62cb573f149c24d90205d26fb139cf7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628651"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)

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

* W programie Visual Studio aplikacji usługa Azure BizTalk Services: Aby pomyślnie wdrożyć aplikacji usługi BizTalk w programie Visual Studio na platformie Azure, możesz wprowadzić nazwę wystawcy kontroli dostępu i klucz wydawcy. 
* Usługa Azure portalu usługi BizTalk Services: Podczas tworzenia usługi BizTalk i Otwieranie portalu usługi BizTalk Services, Twoja nazwa wystawcy kontroli dostępu i klucz wydawcy są automatycznie rejestrowane dla wdrożenia za pomocą tej samej wartości kontroli dostępu.

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
1. Zaloguj się w [Portalu Azure](http://portal.azure.com).
2. Wyszukaj **usługi Service Bus**i wybierz przestrzeń nazw. 
3. Otwórz **zasady dostępu współdzielonego** właściwości, wybierz zasady i wyświetlić **parametry połączenia** dla nazwy i wartości klucza.  

## <a name="next"></a>Następne kroki
Dodatkowe tematy usługi Azure BizTalk Services:

* [Instalowanie zestawu SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Samouczki: Azure BizTalk Services —](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Usługa Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Zobacz też
* [Porady: Konfigurowanie tożsamości usługi za pomocą usługi zarządzania usługi ACS](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Usługa BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Usługa BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

