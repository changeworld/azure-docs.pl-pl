---
title: Konfigurowanie niestandardowej nazwy domeny w usługach w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak udostępnić aplikację lub dane platformy Azure do Internetu w domenie niestandardowej, konfigurując ustawienia DNS.  W tych przykładach użyto witryny Azure portal.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: b1f75a5f7a97907bf5b8bb460ff2df420d053f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386803"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure
Podczas tworzenia usługi w chmurze platforma Azure przypisuje ją do poddomeny **cloudapp.net**. Jeśli na przykład usługa w chmurze nosi nazwę "contoso", użytkownicy będą `http://contoso.cloudapp.net`mogli uzyskiwać dostęp do aplikacji pod adresem URL, takim jak . Platforma Azure przypisuje również wirtualny adres IP.

Można jednak również udostępnić aplikację na własną nazwę domeny, takich jak **contoso.com**. W tym artykule wyjaśniono, jak zarezerwować lub skonfigurować niestandardową nazwę domeny dla ról sieci Web usługi w chmurze.

Czy już rozumiesz, czym są rekordy CNAME i A? [Przeskocz obok wyjaśnienia](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedury w tym zadaniu dotyczą usług w chmurze azure. W przypadku usług app services zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na usługi Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). W przypadku kont magazynu zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob platformy Azure](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Szybsze działania — skorzystaj z nowego [przewodnika platformy](https://support.microsoft.com/kb/2990804)Azure!  To sprawia, że skojarzenie niestandardowej nazwy domeny i zabezpieczania komunikacji (SSL) z usługami w chmurze azure lub witryn platformy Azure w sieci Web przystosowywanie.
> 
> 

## <a name="understand-cname-and-a-records"></a>Poznaj rekordy CNAME i A
CNAME (lub rekordy aliasów) i rekordy A umożliwiają skojarzenie nazwy domeny z określonym serwerem (lub usługą w tym przypadku), jednak działają one inaczej. Istnieją również pewne szczególne zagadnienia podczas korzystania z rekordów z usługami Azure Cloud, które należy wziąć pod uwagę przed podjęciem decyzji, które mają być używane.

### <a name="cname-or-alias-record"></a>Rekord CNAME lub Alias
Rekord CNAME mapuje *określoną* domenę, taką jak **contoso.com** lub **\.www contoso.com,** na kanoniczną nazwę domeny. W takim przypadku kanoniczna nazwa domeny to nazwa domeny **[myapp].cloudapp.net** aplikacji hosta azure. Po utworzeniu CNAME tworzy alias dla **[myapp].cloudapp.net**. Wpis CNAME zostanie automatycznie rozpoznany na adres IP usługi **[myapp].cloudapp.net),** więc jeśli adres IP usługi w chmurze ulegnie zmianie, nie trzeba podejmować żadnych działań.

> [!NOTE]
> Niektóre rejestratory domen zezwalają na mapowanie poddomen tylko podczas korzystania\.z rekordu CNAME, takiego jak www contoso.com, a nie nazw głównych, takich jak contoso.com. Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz dokumentację dostarczoną przez rejestratora, [wpis w Wikipedii w rekordzie CNAME](https://en.wikipedia.org/wiki/CNAME_record)lub dokument [IETF Domain Names - Implementacja i specyfikacja.](https://tools.ietf.org/html/rfc1035)

### <a name="a-record"></a>Rekord
Rekord *A* mapuje domenę, taką jak **contoso.com** lub **www\.contoso.com**lub *domenę wieloznaczną,* taką jak ** \*contoso.com,** na adres IP. W przypadku usługi Azure Cloud Service wirtualny adres IP usługi. Tak więc główną zaletą rekordu A nad rekordem CNAME jest to, że \*można mieć jeden wpis, który używa symbolu wieloznacznego, takiego jak **.contoso.com**, który obsługiwałby żądania dla wielu poddomen, takich jak **mail.contoso.com**, **login.contoso.com**lub **www\.contso.com**.

> [!NOTE]
> Ponieważ rekord A jest mapowany na statyczny adres IP, nie może automatycznie rozwiązać zmian adresu IP usługi w chmurze. Adres IP używany przez usługę w chmurze jest przydzielany przy pierwszym wdrożeniu w pustym gnieździe (produkcyjnym lub przejściowym). Jeśli usuniesz wdrożenie dla gniazda, adres IP jest zwalniany przez platformę Azure i wszelkie przyszłe wdrożenia do gniazda mogą mieć nowy adres IP.
> 
> Wygodnie adres IP danego gniazda wdrożenia (produkcyjnego lub przejściowego) jest zachowywany podczas wymiany między wdrożeniami przejściowymi i produkcyjnymi lub podczas uaktualniania w miejscu istniejącego wdrożenia. Aby uzyskać więcej informacji na temat wykonywania tych akcji, zobacz [Jak zarządzać usługami w chmurze](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Dodawanie rekordu CNAME dla domeny niestandardowej
Aby utworzyć rekord CNAME, należy dodać nowy wpis w tabeli DNS dla domeny niestandardowej przy użyciu narzędzi dostarczonych przez rejestratora. Każdy rejestrator ma podobną, ale nieco inną metodę określania rekordu CNAME, ale pojęcia są takie same.

1. Użyj jednej z tych metod, aby znaleźć nazwę domeny **cloudapp.net** przypisaną do usługi w chmurze.

   * Zaloguj się do [witryny Azure portal], wybierz usługę w chmurze, spójrz na **sekcję Przegląd,** a następnie znajdź wpis **adresu URL witryny.**

       ![sekcja szybkiego spojrzenia z adresem URL witryny][csurl]

       **Lub**
   * Zainstaluj i skonfiguruj [program Azure Powershell,](/powershell/azure/overview)a następnie użyj następującego polecenia:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Zapisz nazwę domeny używaną w adresie URL zwrócony przez jedną z metod, ponieważ będzie jej potrzebować podczas tworzenia rekordu CNAME.
2. Zaloguj się do witryny internetowej rejestratora DNS i przejdź do strony, aby zarządzać systemem DNS. Poszukaj łączy lub obszarów witryny oznaczonych jako **Domain Name**, **DNS**lub Name **Server Management**.
3. Teraz znajdź miejsce, w którym możesz wybrać lub wprowadzić CNAME. Może być trzeba wybrać typ rekordu z listy rozwijanej lub przejść do strony ustawień zaawansowanych. Należy poszukać słów **CNAME**, **Alias**lub **Subdomeny**.
4. Należy również podać alias domeny lub subdomeny dla CNAME, na przykład **www,** jeśli chcesz utworzyć alias dla **customdomain.com www\.**. Jeśli chcesz utworzyć alias dla domeny głównej, może on**\@** być wymieniony jako symbol ' w narzędziach DNS rejestratora.
5. Następnie należy podać nazwę hosta kanonicznego, która jest **domeną cloudapp.net** aplikacji w tym przypadku.

Na przykład następujący rekord CNAME przekazuje cały ruch z **contoso.com\.** do **contoso.cloudapp.net**, niestandardowej nazwy domeny wdrożonej aplikacji:

| Alias/Nazwa hosta/Poddomena | Domena kanoniczna |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Użytkownik **www\.contoso.com** nigdy nie zobaczy prawdziwego hosta (contoso.cloudapp.net), więc proces przekazywania jest niewidoczny dla użytkownika końcowego.
> 
> Powyższy przykład dotyczy tylko ruchu na poddomenie **www.** Ponieważ nie można używać symboli wieloznacznych z rekordami CNAME, należy utworzyć jedną nazwę CNAME dla każdej domeny/poddomeny. Jeśli chcesz skierować ruch z poddomen, takich jak *.contoso.com, na adres cloudapp.net, możesz skonfigurować **wpis przekierowania adresu URL** lub **wpisu url do przodu** w ustawieniach DNS lub utworzyć rekord A.

## <a name="add-an-a-record-for-your-custom-domain"></a>Dodawanie rekordu A dla domeny niestandardowej
Aby utworzyć rekord A, należy najpierw znaleźć wirtualny adres IP usługi w chmurze. Następnie dodaj nowy wpis w tabeli DNS dla domeny niestandardowej przy użyciu narzędzi dostarczonych przez rejestratora. Każdy rejestrator ma podobną, ale nieco inną metodę określania rekordu A, ale pojęcia są takie same.

1. Użyj jednej z następujących metod, aby uzyskać adres IP usługi w chmurze.

   * Zaloguj się do [witryny Azure portal], wybierz usługę w chmurze, spójrz na **sekcję Przegląd,** a następnie znajdź wpis **Publiczne adresy IP.**

       ![sekcja szybkiego spojrzenia przedstawiająca program VIP][vip]

       **Lub**
   * Zainstaluj i skonfiguruj [program Azure Powershell,](/powershell/azure/overview)a następnie użyj następującego polecenia:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Zapisz adres IP, ponieważ będzie on potrzebny podczas tworzenia rekordu A.
2. Zaloguj się do witryny internetowej rejestratora DNS i przejdź do strony, aby zarządzać systemem DNS. Poszukaj łączy lub obszarów witryny oznaczonych jako **Domain Name**, **DNS**lub Name **Server Management**.
3. Teraz znajdź miejsce, w którym możesz wybrać lub wprowadzić rekord A. Może być trzeba wybrać typ rekordu z listy rozwijanej lub przejść do strony ustawień zaawansowanych.
4. Wybierz lub wprowadź domenę lub poddomenę, która będzie używać tego rekordu A. Na przykład wybierz **www,** jeśli chcesz utworzyć alias dla **customdomain.com www\.**. Jeśli chcesz utworzyć symbol wieloznaczny dla wszystkich poddomen, wprowadź "*****". Obejmie to wszystkie poddomeny, takie jak **mail.customdomain.com,** **login.customdomain.com**i **\.www customdomain.com.**

    Jeśli chcesz utworzyć rekord A dla domeny głównej, może on**\@** być wymieniony jako symbol ' w narzędziach DNS rejestratora.
5. Wprowadź adres IP usługi w chmurze w podanym polu. Spowoduje to skojarzenie wpisu domeny używanego w rekordzie A z adresem IP wdrożenia usługi w chmurze.

Na przykład następujący rekord A przekazuje cały ruch z **contoso.com** do **137.135.70.239**, adres IP wdrożonej aplikacji:

| Nazwa hosta/poddomena | Adres IP |
| --- | --- |
| \@ |137.135.70.239 |

W tym przykładzie pokazano tworzenie rekordu A dla domeny głównej. Jeśli chcesz utworzyć wpis z symbolami wieloznacznych obejmującym wszystkie poddomeny, należy wprowadzić "*****" jako poddomenę.

> [!WARNING]
> Adresy IP na platformie Azure są domyślnie dynamiczne. Prawdopodobnie będziesz chciał użyć [zastrzeżonego adresu IP,](../virtual-network/virtual-networks-reserved-public-ip.md) aby upewnić się, że twój adres IP nie ulegnie zmianie.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Jak zarządzać usługami w chmurze](cloud-services-how-to-manage-portal.md)
* [Jak zamapować zawartość usługi CDN na domenę niestandardową](../cdn/cdn-map-content-to-custom-domain.md)
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [certyfikatów ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



