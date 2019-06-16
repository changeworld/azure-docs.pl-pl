---
title: Konfigurowanie niestandardowej nazwy domeny w usługach Cloud Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udostępnić w aplikacji platformy Azure lub danych w Internecie w domenie niestandardowej, konfigurując ustawienia DNS.  Te przykłady użycia witryny Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: e210882cb773718f68e9178cbbce6874c2729744
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063611"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure
Podczas tworzenia usługi w chmurze, platforma Azure przypisuje go do domeny podrzędnej **cloudapp.net**. Na przykład, jeśli usługa w chmurze ma nazwę "contoso", użytkownicy będą mogli korzystać z aplikacji na adres URL podobny `http://contoso.cloudapp.net`. Platforma Azure przypisuje także wirtualnego adresu IP.

Jednak również udostępnić aplikację na własną nazwę domeny, takich jak **contoso.com**. W tym artykule wyjaśniono, jak zarezerwować lub konfigurowanie niestandardowej nazwy domeny dla ról sieci web usługi w chmurze.

Czy już rozumiesz, jakie są CNAME i? [Skok poza wyjaśnienie](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedury przedstawione w tym zadaniu mają zastosowanie do usług Azure Cloud Services. Dla usług App Service, zobacz [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). W przypadku kont magazynu, zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Azure Blob storage](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Pracuj szybciej — skorzystaj z nowego kalkulatora [krok po kroku przewodnik](https://support.microsoft.com/kb/2990804)!  To sprawia, że skojarzenie niestandardowej nazwy domeny i zabezpieczania komunikacji (SSL) przy użyciu usług Azure Cloud Services lub usługi Azure Websites drobiazg.
> 
> 

## <a name="understand-cname-and-a-records"></a>Zrozumienie rekordów CNAME i A
CNAME (lub rekordów aliasów) i rekordy zarówno pozwalają na skojarzenie nazwy domeny z określonym serwerem (lub usługi w tym przypadku) jednak działają one inaczej. Istnieją również kilka zagadnień, korzystając z rekordów z usługami w chmurze platformy Azure, które należy wziąć pod uwagę przed podjęciem decyzji, której mają zostać użyte.

### <a name="cname-or-alias-record"></a>Rekord CNAME, Alias lub
Mapuje rekord CNAME *określonych* domeny, takich jak **contoso.com** lub **www\.contoso.com**, aby wskazywała nazwę domeny kanonicznej. W takim przypadku nazwa kanoniczna domeny jest **.cloudapp [moja_aplikacja] .net** aplikacji hostowanej w nazwie domeny platformy Azure. Po utworzeniu tego rekordu CNAME tworzy alias dla **.cloudapp [moja_aplikacja] .net**. Wpis CNAME zostanie rozpoznana jako adres IP Twojego **.cloudapp [moja_aplikacja] .net** usługi automatycznie, więc jeśli zmieni adres IP usługi w chmurze, nie trzeba podejmować żadnych działań.

> [!NOTE]
> Mapowanie poddomeny przy użyciu rekordu CNAME, takich jak www Zezwalaj tylko niektóre rejestratorów domeny\.contoso.com, a nie nazwy katalogu głównego, np. contoso.com. Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz temat w dokumentacji dostarczonej przez rejestrator, [Wikipedia wpis rekordu CNAME](https://en.wikipedia.org/wiki/CNAME_record), lub [nazw domen IETF — implementacja i specyfikacja](https://tools.ietf.org/html/rfc1035) dokumentu.

### <a name="a-record"></a>Rekord
*A* rekordu mapuje domenę, taką jak **contoso.com** lub **www\.contoso.com**, *lub domeny z symbolami wieloznacznymi* takich jak  **\*. contoso.com**, adres IP. W przypadku usługi Azure Cloud Service, wirtualnego adresu IP usługi. Dlatego główną zaletą rekord za pośrednictwem rekord CNAME jest, że może mieć jeden wpis, który używa symboli wieloznacznych, takich jak \* **. contoso.com**, które będzie takie jak obsługi żądań wiele domen podrzędnych  **mail.contoso.com**, **domeny login.contoso.com**, lub **www\.contso.com**.

> [!NOTE]
> Ponieważ rekord A jest mapowany na statyczny adres IP, nie może automatycznie rozwiązać zmian adres IP usługi w chmurze. Adres IP używany przez usługi w chmurze jest przydzielany podczas pierwszego wdrażania do pustego gniazda (produkcyjnego lub tymczasowego.) Jeśli usuniesz wdrożenia dla gniazda, adres IP jest zwalniany przez platformę Azure i wszystkich przyszłych wdrożeń do gniazda mogą otrzymać nowy adres IP.
> 
> Wygodnie adres IP dane miejsce wdrożenia (produkcyjnego lub tymczasowego) są utrwalane podczas wymiany między przemieszczania i wdrożeń produkcyjnych lub w przypadku uaktualniania w miejscu istniejącego wdrożenia. Aby uzyskać więcej informacji na temat wykonywania tych akcji, zobacz [jak zarządzać usługami cloud services](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Dodaj rekord CNAME dla domeny niestandardowej
Aby utworzyć rekord CNAME, należy dodać nowy wpis w tabeli DNS dla domeny niestandardowej za pomocą narzędzi dostarczanych przez rejestrator. Każdy rejestrator ma nieco inną metodę określania rekordu CNAME, ale podstawowe koncepcje są takie same.

1. Użyj jednej z następujących metod, aby znaleźć **. cloudapp.net** nazwy domeny przypisany do usługi w chmurze.

   * Zaloguj się do [Azure Portal], wybierz usługę w chmurze, Przyjrzyj się **Przegląd** sekcji, a następnie znajdź **adres URL witryny** wpisu.

       ![Przegląd sekcji, w której adres URL witryny][csurl]

       **OR**
   * Instalowanie i konfigurowanie [programu Azure Powershell](/powershell/azure/overview), a następnie za pomocą następującego polecenia:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Zapisz nazwę domeny używanej w adresie URL zwrócone przez jednej z metod, ponieważ będzie on potrzebny podczas tworzenia rekordu CNAME.
2. Zaloguj się do rejestratora DNS witryny sieci Web i przejdź do strony zarządzania DNS. Znajdź łącza lub obszarów witryny oznaczone jako **nazwy domeny**, **DNS**, lub **Zarządzanie serwerem nazw**.
3. Teraz można znaleźć, gdzie wybierz lub wprowadź firmy CNAME. Może być konieczne wybierz typ rekordu, z listy rozwijanej lub przejdź do strony ustawień zaawansowanych. Należy szukać wyrazy **CNAME**, **Alias**, lub **poddomen**.
4. Należy również podać domeny lub poddomeny alias dla tego rekordu CNAME, takich jak **www** Jeśli chcesz utworzyć alias **www\.customdomain.com**. Jeśli chcesz utworzyć alias dla domeny głównej, może być dostępna jako " **\@** " symbol w narzędziach DNS u rejestratora swojej firmy.
5. Następnie musisz podać nazwę kanoniczną hosta, którego Twoja aplikacja **cloudapp.net** domeny, w tym przypadku.

Na przykład, następujący rekord CNAME przekazuje cały ruch z **www\.contoso.com** do **contoso.cloudapp.net**, nazwa domeny niestandardowej w Twojej wdrożonej aplikacji:

| Nazwa aliasu/hosta/poddomeny | Canonical domeny |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Obiekt odwiedzający **www\.contoso.com** nigdy nie zobaczą true hosta (contoso.cloudapp.net), dzięki czemu proces przekazywania jest niewidoczne dla użytkownika końcowego.
> 
> W powyższym przykładzie dotyczy tylko ruchu na **www** poddomeny. Ponieważ rekordy CNAME nie można używać symboli wieloznacznych, należy utworzyć jeden rekord CNAME dla każdej domeny/poddomeny. Jeśli chcesz kierować ruch z domen podrzędnych, takich jak *. contoso.com, adres cloudapp.net możesz skonfigurować **przekierowywania adresu URL** lub **adres URL do przodu** zapis w ustawieniach DNS lub utworzyć rekord.

## <a name="add-an-a-record-for-your-custom-domain"></a>Dodaj rekord A dla domeny niestandardowej
Aby utworzyć rekord A, możesz znaleźć wirtualnego adresu IP usługi w chmurze. Następnie dodaj nowy wpis w tabeli DNS dla domeny niestandardowej za pomocą narzędzi dostarczanych przez rejestrator. Każdy rejestrator ma nieco inną metodę określania rekord A, ale podstawowe koncepcje są takie same.

1. Aby uzyskać adres IP usługi w chmurze, użyj jednej z następujących metod.

   * Zaloguj się do [Azure Portal], wybierz usługę w chmurze, Przyjrzyj się **Przegląd** sekcji, a następnie znajdź **publiczne adresy IP** wpisu.

       ![Przegląd sekcji, w której adres VIP][vip]

       **OR**
   * Instalowanie i konfigurowanie [programu Azure Powershell](/powershell/azure/overview), a następnie za pomocą następującego polecenia:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Zapisz adres IP, ponieważ będzie on potrzebny podczas tworzenia rekordu.
2. Zaloguj się do rejestratora DNS witryny sieci Web i przejdź do strony zarządzania DNS. Znajdź łącza lub obszarów witryny oznaczone jako **nazwy domeny**, **DNS**, lub **Zarządzanie serwerem nazw**.
3. Teraz można znaleźć, którym można wybrać lub wprowadzić rekord. Może być konieczne wybierz typ rekordu, z listy rozwijanej lub przejdź do strony ustawień zaawansowanych.
4. Wybierz lub wprowadź domeny lub poddomeny, który będzie używał tego rekordu A. Na przykład wybierz **www** Jeśli chcesz utworzyć alias **www\.customdomain.com**. Jeśli chcesz utworzyć wpis symboli wieloznacznych dla wszystkich domen podrzędnych, wprowadź "***". Wszystkimi domenami podrzędnymi będzie on zawierał takie jak **mail.customdomain.com**, **login.customdomain.com**, i **www\.customdomain.com**.

    Jeśli chcesz utworzyć rekord A dla domeny katalogu głównego, może być wyświetlany jako " **\@** " symbol w narzędziach DNS u rejestratora swojej firmy.
5. Wprowadź adres IP, usługi w chmurze, w polu podana. Skojarzenie wpis domeny używane w rekordzie o adresie IP wdrażania usługi w chmurze.

Na przykład następujący rekord przekazuje cały ruch z **contoso.com** do **137.135.70.239**, adres IP Twojej wdrożonej aplikacji:

| Nazwa hosta/poddomeny | Adres IP |
| --- | --- |
| \@ |137.135.70.239 |

W przykładzie pokazano tworzenie rekordu A dla domeny katalogu głównego. Jeśli chcesz utworzyć wpis symboli wieloznacznych, aby pokrywał wszystkimi domenami podrzędnymi, wpisz "***" jako domenę podrzędną.

> [!WARNING]
> Adresy IP na platformie Azure są dynamiczne domyślnie. Będzie prawdopodobnie chcesz użyć [zarezerwowany adres IP](../virtual-network/virtual-networks-reserved-public-ip.md) aby upewnić się, że adres IP nie zmienia się.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Jak zarządzać usługami Cloud Services](cloud-services-how-to-manage-portal.md)
* [Jak zamapować zawartość usługi CDN na domenę niestandardową](../cdn/cdn-map-content-to-custom-domain.md)
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [certyfikaty ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
