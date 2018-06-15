---
title: Nazwa wystawcy i klucza wystawcy usługi BizTalk Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać Nazwa wystawcy i klucza wystawcy dla usługi Service Bus lub kontroli dostępu (ACS) w usługach BizTalk. MABS, WABS
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
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24103433"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure usługi BizTalk Services używa nazwy wystawcy magistrali usługi i klucza wystawcy i nazwę wystawcy kontroli dostępu i klucza wystawcy. W szczególności:

| Zadanie | Która nazwa wystawcy i klucza wystawcy |
| --- | --- |
| Wdrażanie aplikacji w programie Visual Studio |Nazwa wystawcy kontroli dostępu i klucza wystawcy |
| Konfigurowanie portalu Azure BizTalk Services |Nazwa wystawcy kontroli dostępu i klucza wystawcy |
| Tworzenie obiektu LOB przekaźników z usługami karty BizTalk w programie Visual Studio |Nazwa wystawcy magistrali usługi i klucza wystawcy |

W tym temacie przedstawiono kroki, aby pobrać nazwę wystawcy i klucza wystawcy. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nazwa wystawcy kontroli dostępu i klucza wystawcy
Nazwa wystawcy kontroli dostępu i klucz wystawcy, są używane przez następujące czynności:

* Aplikacja usługi BizTalk Azure utworzone w programie Visual Studio: Aby pomyślnie wdrożyć usługę BizTalk aplikacji w programie Visual Studio na platformie Azure, możesz wprowadzić nazwę wystawcy kontroli dostępu i klucza wystawcy. 
* Portalu Azure usługi BizTalk: Podczas tworzenia usługi BizTalk i otwórz Portal usługi BizTalk, nazwa wystawcy kontroli dostępu, a klucz wystawcy automatycznie zarejestrowanych dla wdrożeń o tej samej wartości kontroli dostępu.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Nazwa wystawcy kontroli dostępu i klucza wystawcy

Do usług ACS jest używany do uwierzytelniania i uzyskać wartości nazwy wystawcy i klucza wystawcy, ogólne kroki obejmują:

1. Zainstaluj [poleceń cmdlet programu Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Dodaj konto platformy Azure:`Add-AzureAccount`
3. Zwraca nazwę Twojej subskrypcji:`get-azuresubscription`
4. Wybierz subskrypcję:`select-azuresubscription <name of your subscription>` 
5. Tworzenie nowej przestrzeni nazw:`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Przykład:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Po utworzeniu nowej przestrzeni nazw usługi ACS, (co może potrwać kilka minut), wartości Nazwa wystawcy i klucza wystawcy są wymienione w ciągu połączenia: 

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

Więcej na temat [AzureSBNamespace nowy](https://msdn.microsoft.com/library/dn495165.aspx) polecenia cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nazwa wystawcy magistrali usługi i klucza wystawcy
Nazwa wystawcy magistrali usługi i klucza wystawcy, są używane przez usługi karty BizTalk. W projekcie usługi BizTalk Services w programie Visual Studio usługi karty BizTalk są używane do nawiązania połączenia lokalnego systemu — biznesowych (LOB). Nawiązać połączenie, Utwórz LOB przekazywania i wprowadź szczegóły systemu LOB. W ten sposób można także wprowadzić nazwy wystawcy magistrali usługi i klucza wystawcy.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Pobieranie nazwy wystawcy magistrali usługi i klucza wystawcy
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com).
2. Wyszukaj **usługi Service Bus**i wybierz przestrzeni nazw. 
3. Otwórz **zasady dostępu współużytkowanego** właściwości, wybierz zasady i wyświetlić **ciąg połączenia** dla nazwy i wartości klucza.  

## <a name="next"></a>Następne kroki
Dodatkowe tematy usługi BizTalk Azure:

* [Instalowanie usług Azure BizTalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Samouczki: Usługi Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Usługi Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Zobacz też
* [Porady: Konfigurowanie tożsamości usługi za pomocą usługi zarządzania ACS](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Usługi BizTalk Services: Developer, podstawowa, standardowa i Premium Edition wykresu](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Usługi BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

