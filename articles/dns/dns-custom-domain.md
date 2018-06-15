---
title: Integrowanie usługi Azure DNS z zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DNS wzdłuż zapewnienie DNS dla zasobów platformy Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: kumud
ms.openlocfilehash: cbc769cd7356b3057fd2aae295071b04d2e40d91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2018
ms.locfileid: "27994386"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Użyj usługi Azure DNS, aby określić ustawienia domeny niestandardowej dla usługi Azure

Usługa DNS platformy Azure udostępnia DNS dla domeny niestandardowej dla każdego z zasobów platformy Azure czy obsługa domen niestandardowych lub które mają w pełni kwalifikowaną nazwą domeny (FQDN). Przykładem jest aplikacja sieci web platformy Azure i ma do niego dostęp przez użytkowników przy użyciu contoso.com lub www.contoso.com jako nazwy FQDN. W tym artykule przedstawiono konfigurowanie usługi Azure przy użyciu usługi Azure DNS dotyczące korzystania z niestandardowej domeny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć usługi Azure DNS dla domeny niestandardowej, należy najpierw Delegowanie domeny do usługi Azure DNS. Odwiedź stronę [Delegowanie domeny do usługi Azure DNS](./dns-delegate-domain-azure-dns.md) instrukcje na temat sposobu konfigurowania serwerów nazw dla delegowania. Po oddelegowaniu domenę do strefy DNS platformy Azure będą mogli skonfigurować rekordy DNS potrzebne.

Można skonfigurować niestandardowych lub domeny niestandardowej dla [aplikacji funkcji Azure](#azure-function-app), [publicznego adresu IP, adresy](#public-ip-address), [usługi aplikacji (aplikacje sieci Web)](#app-service-web-apps), [magazynuobiektówBlob](#blob-storage), i [usługi Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplikacji Azure — funkcja

Aby skonfigurować niestandardową domenę na potrzeby aplikacji funkcji platformy Azure, oraz konfigurację w funkcji aplikacja zostaje utworzony rekord CNAME.
 
Przejdź do **innych** > **aplikacji funkcji** i wybierz aplikację funkcji. Kliknij przycisk **funkcji platformy** i w obszarze **sieci** kliknij **domen niestandardowych**.

![Funkcja bloku aplikacji](./media/dns-custom-domain/functionapp.png)

Zanotuj bieżący adres url na **domen niestandardowych** bloku, ten adres jest używany jako alias dla rekord DNS utworzony.

![Blok domeny niestandardowej](./media/dns-custom-domain/functionshostname.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestawu rekordów**. Wypełnij następujące informacje w **dodać zestaw rekordów** bloku i kliknij przycisk **OK** go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | myfunctionapp        | Ta wartość wraz z etykieta nazwy domeny jest nazwa FQDN nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME jest za pomocą aliasu.        |
|CZAS WYGAŚNIĘCIA     | 1        | 1 jest używany przez godzinę        |
|Czas wygaśnięcia jednostki     | Godziny        | Godziny są używane jako wartość czasu         |
|Alias     | adatumfunction.azurewebsites.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS adatumfunction.azurewebsites.net domyślne do aplikacji funkcji.        |

Przejdź z powrotem do aplikacji funkcji, kliknij przycisk **funkcji platformy**, a następnie w obszarze **sieci** kliknij **domen niestandardowych**, następnie w obszarze **nazwy hostów** kliknij **+ Dodaj nazwę hosta**.

Na **dodać nazwę hosta** bloku, wprowadź rekord CNAME w **hostname** pole tekstowe, kliknij przycisk **weryfikacji**. Jeśli rekord był w stanie znaleźć **dodać nazwę hosta** pojawi się przycisk. Kliknij przycisk **dodać nazwę hosta** Aby dodać alias.

![host name blok dodawania aplikacji funkcji](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Publiczny adres IP

Aby skonfigurować niestandardową domenę usług korzystających z publicznym adresem IP adresów zasobów, takich jak bramy aplikacji, usługi równoważenia obciążenia, usługa w chmurze, maszyn wirtualnych Menedżera zasobów i klasycznych maszyn wirtualnych, rekord CNAME rejestrowania używane.

Przejdź do **sieci** > **publicznego adresu IP**, wybierz zasób publicznego adresu IP i kliknij przycisk **konfiguracji**. Zapisania wyświetlany adres IP.

![Blok publicznego adresu ip](./media/dns-custom-domain/publicip.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestawu rekordów**. Wypełnij następujące informacje w **dodać zestaw rekordów** bloku i kliknij przycisk **OK** go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | mywebserver        | Ta wartość wraz z etykieta nazwy domeny jest nazwa FQDN nazwy domeny niestandardowej.        |
|Typ     | A        | Przy użyciu rekordu A jako zasobu jest adresem IP.        |
|CZAS WYGAŚNIĘCIA     | 1        | 1 jest używany przez godzinę        |
|Czas wygaśnięcia jednostki     | Godziny        | Godziny są używane jako wartość czasu         |
|Adres IP     | <your ip address>       | Publiczny adres IP.|

![Utwórz rekord a.](./media/dns-custom-domain/arecord.png)

Po utworzeniu rekordu A, uruchom `nslookup` można sprawdzić poprawności rekordu jest rozpoznawana.

![wyszukiwania dns publicznego adresu ip](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Usługi aplikacji (aplikacje sieci Web)

Poniższe kroki przedstawiają skonfigurowanie domeny niestandardowej dla aplikacji sieci web usługi aplikacji.

Przejdź do **sieci Web i mobilnych** > **usługi aplikacji** i wybierz zasób konfigurowania niestandardowej nazwy domeny i kliknij przycisk **domen niestandardowych**.

Zanotuj bieżący adres url na **domen niestandardowych** bloku, ten adres jest używany jako alias dla rekord DNS utworzony.

![domen niestandardowych bloku](./media/dns-custom-domain/url.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestawu rekordów**. Wypełnij następujące informacje w **dodać zestaw rekordów** bloku i kliknij przycisk **OK** go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | mywebserver        | Ta wartość wraz z etykieta nazwy domeny jest nazwa FQDN nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME jest za pomocą aliasu. Jeśli zasób jest używany adres IP, mogą być wykorzystane rekord A.        |
|CZAS WYGAŚNIĘCIA     | 1        | 1 jest używany przez godzinę        |
|Czas wygaśnięcia jednostki     | Godziny        | Godziny są używane jako wartość czasu         |
|Alias     | webserver.azurewebsites.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS webserver.azurewebsites.net domyślne do aplikacji sieci web.        |


![Utwórz rekord CNAME](./media/dns-custom-domain/createcnamerecord.png)

Przejdź z powrotem do usługi app service, który jest skonfigurowany dla nazwy domeny niestandardowej. Kliknij przycisk **domen niestandardowych**, następnie kliknij przycisk **Hostnames**. Aby dodać utworzony rekord CNAME, kliknij przycisk **+ Dodaj hostname**.

![rysunek 1](./media/dns-custom-domain/figure1.png)

Po zakończeniu procesu uruchamiania **nslookup** do sprawdzania poprawności rozpoznawanie nazw działa.

![rysunek 1](./media/dns-custom-domain/finalnslookup.png)

Aby dowiedzieć się więcej o mapowaniu niestandardową domenę do usługi App Service, odwiedź stronę [mapowania istniejącej nazwy DNS niestandardowej aplikacji sieci Web Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Jeśli potrzebujesz kupić domenę niestandardową, odwiedź stronę [kupić niestandardowej nazwy domeny dla aplikacji sieci Web Azure](../app-service/custom-dns-web-site-buydomains-web-app.md) Aby dowiedzieć się więcej o domenach usługi aplikacji.

## <a name="blob-storage"></a>Blob Storage

Poniższe kroki przedstawiają Konfigurowanie rekord CNAME dla konta magazynu obiektów blob przy użyciu metody asverify. Ta metoda gwarantuje, że nie istnieje bez przestojów.

Przejdź do **magazynu** > **kont magazynu**, wybierz konto magazynu i kliknij przycisk **domeny niestandardowe**. Zapisania nazwy FQDN w kroku 2, ta wartość jest używana do utworzenia pierwszego rekordu CNAME

![domeny niestandardowej magazynu obiektów blob](./media/dns-custom-domain/blobcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestawu rekordów**. Wypełnij następujące informacje w **dodać zestaw rekordów** bloku i kliknij przycisk **OK** go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | asverify.mystorageaccount        | Ta wartość wraz z etykieta nazwy domeny jest nazwa FQDN nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME jest za pomocą aliasu.        |
|CZAS WYGAŚNIĘCIA     | 1        | 1 jest używany przez godzinę        |
|Czas wygaśnięcia jednostki     | Godziny        | Godziny są używane jako wartość czasu         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS asverify.adatumfunctiona9ed.blob.core.windows.net domyślne konto magazynu.        |

Przejdź z powrotem do swojego konta magazynu, klikając **magazynu** > **kont magazynu**, wybierz konto magazynu i kliknij przycisk **domeny niestandardowe**. Wpisz alias został utworzony bez prefiksu asverify w polu tekstowym wyboru ** Użyj pośredniej weryfikacji CNAME, a następnie kliknij przycisk **zapisać**. Po wykonaniu tego kroku, wróć do strefy DNS i utworzyć rekord CNAME, bez prefiksu asverify.  Po tym etapie jest bezpiecznie usunąć rekord CNAME z prefiksem cdnverify.

![domeny niestandardowej magazynu obiektów blob](./media/dns-custom-domain/indirectvalidate.png)

Sprawdź poprawność rozpoznawania nazw DNS, uruchamiając`nslookup`

Aby dowiedzieć się więcej na temat mapowanie niestandardową domenę do punktu końcowego magazynu obiektów blob odwiedź [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Usługa Azure CDN

Poniższe kroki przedstawiają Konfigurowanie rekord CNAME dla punktu końcowego usługi CDN przy użyciu metody cdnverify. Ta metoda gwarantuje, że nie istnieje bez przestojów.

Przejdź do **sieci** > **profilów usługi CDN**, a następnie wybierz profil CDN i kliknij przycisk **punkty końcowe** w obszarze **ogólne**.

Wybierz punkt końcowy pracy z, kliknij przycisk **+ domeny niestandardowe**. Uwaga **hosta punktu końcowego** ta wartość jest rekord, który wskazuje rekord CNAME.

![Niestandardowa domena usługi CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestawu rekordów**. Wypełnij następujące informacje w **dodać zestaw rekordów** bloku i kliknij przycisk **OK** go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | cdnverify.mycdnendpoint        | Ta wartość wraz z etykieta nazwy domeny jest nazwa FQDN nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME jest za pomocą aliasu.        |
|CZAS WYGAŚNIĘCIA     | 1        | 1 jest używany przez godzinę        |
|Czas wygaśnięcia jednostki     | Godziny        | Godziny są używane jako wartość czasu         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS cdnverify.adatumcdnendpoint.azureedge.net domyślne konto magazynu.        |

Przejdź wstecz do punktu końcowego CDN, klikając **sieci** > **profilów usługi CDN**i wybierz profil CDN. Kliknij przycisk **+ domeny niestandardowe** i wprowadź aliasu rekordu CNAME bez prefiksu cdnverify i kliknij przycisk **Dodaj**.

Po wykonaniu tego kroku, wróć do strefy DNS i utworzyć rekord CNAME, bez prefiksu cdnverify.  Po tym etapie jest bezpiecznie usunąć rekord CNAME z prefiksem cdnverify. Więcej informacji o sieci CDN i sposobie konfigurowania domeny niestandardowej bez kroku pośredniego rejestracji można znaleźć [zawartości mapy usługi Azure CDN na domenę niestandardową](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [skonfigurować wstecznego DNS dla usługi hostowanej na platformie Azure](dns-reverse-dns-for-azure-services.md).