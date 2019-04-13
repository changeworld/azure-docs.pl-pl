---
title: Integracja usługi Azure DNS z zasobami platformy Azure
description: Dowiedz się, jak używać usługi Azure DNS, wzdłuż, aby zapewnić DNS dla zasobów platformy Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: 5c098c6c22b079d586c0bd808df9af4a737c17a8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521856"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Podaj ustawienia domeny niestandardowej dla usługi platformy Azure przy użyciu usługi Azure DNS

Usługa Azure DNS zapewnia DNS dla domeny niestandardowej dla każdego z zasobów platformy Azure, obsługę domen niestandardowych lub które zostały w pełni kwalifikowaną nazwę domeny (FQDN). Przykładem jest aplikację internetową platformy Azure i chcesz, aby do niego dostęp przez użytkowników za pomocą contoso.com lub www\.domeny contoso.com jako nazwy FQDN. W tym artykule przedstawiono konfigurowanie usługi platformy Azure przy użyciu usługi Azure DNS dotyczące używania domen niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać usługi Azure DNS dla domeny niestandardowej, musisz delegować domenę do usługi Azure DNS. Odwiedź stronę [delegować domenę do usługi Azure DNS](./dns-delegate-domain-azure-dns.md) instrukcje dotyczące sposobu konfigurowania serwerów nazw dla celów delegacji. Po domeny jest delegowane do strefy DNS platformy Azure, jesteś w stanie skonfigurować rekordy DNS niezbędne.

Możesz skonfigurować znaczących lub domeny niestandardowej dla [aplikacji funkcji platformy Azure](#azure-function-app), [publiczne adresy IP](#public-ip-address), [usługi App Service (aplikacje sieci Web)](#app-service-web-apps), [Blob storage](#blob-storage), i [usługi Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplikacja funkcji platformy Azure

Aby skonfigurować domeny niestandardowej na potrzeby aplikacji funkcji platformy Azure, rekord CNAME powstaje oraz konfigurację w samej aplikacji funkcji.
 
Przejdź do **aplikacji funkcji** i wybierz aplikację funkcji. Kliknij przycisk **funkcje platformy** i w obszarze **sieć** kliknij **domen niestandardowych**.

![Blok aplikacji — funkcja](./media/dns-custom-domain/functionapp.png)

Należy pamiętać, bieżący adres url na **domen niestandardowych** bloku, ten adres jest używany jako alias dla rekordu DNS utworzonego.

![Blok domeny niestandardowej](./media/dns-custom-domain/functionshostname.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestaw rekordów**. Wypełnij następujące informacje w **Dodawanie zestawu rekordów** bloku i kliknij przycisk **OK** do jego utworzenia.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | myfunctionapp        | Ta wartość wraz z etykieta nazwy domeny jest nazwą FQDN dla nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME w systemie jest za pomocą aliasu.        |
|Czas wygaśnięcia     | 1        | 1 jest używana przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiaru czasu         |
|Alias     | adatumfunction.azurewebsites.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS adatumfunction.azurewebsites.net domyślne do aplikacji funkcji.        |

Przejdź z powrotem do aplikacji funkcji, kliknij przycisk **funkcje platformy**, a następnie w obszarze **sieć** kliknij **domen niestandardowych**, a następnie w obszarze **niestandardowe nazwy hostów** kliknij **+ Dodaj nazwę hosta**.

Na **Dodaj nazwę hosta** bloku wprowadzić rekord CNAME w **hostname** polu tekstowym i kliknij przycisk **weryfikacji**. Jeśli zostanie znaleziony rekord, **Dodaj nazwę hosta** pojawi się przycisk. Kliknij przycisk **Dodaj nazwę hosta** Aby dodać alias.

![host name blok dodawania aplikacji funkcji](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Publiczny adres IP

Aby skonfigurować domenę niestandardową dla usług korzystających z publicznym adresem IP adresów zasobów, takich jak usługa Application Gateway, moduł równoważenia obciążenia, usługi w chmurze, maszyny wirtualne usługi Resource Manager i klasycznych maszyn wirtualnych, rekord jest używany.

Przejdź do **sieć** > **publiczny adres IP**, wybierz zasób publicznego adresu IP i kliknij przycisk **konfiguracji**. Zapisania, adres IP wyświetlany.

![Blok publicznego adresu ip](./media/dns-custom-domain/publicip.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestaw rekordów**. Wypełnij następujące informacje w **Dodawanie zestawu rekordów** bloku i kliknij przycisk **OK** do jego utworzenia.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | mywebserver        | Ta wartość wraz z etykieta nazwy domeny jest nazwą FQDN dla nazwy domeny niestandardowej.        |
|Typ     | A        | Tylko zasób jest adres IP, należy użyć rekordu A.        |
|Czas wygaśnięcia     | 1        | 1 jest używana przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiaru czasu         |
|Adres IP     | `<your ip address>`       | Publiczny adres IP.|

![Tworzenie rekordu A](./media/dns-custom-domain/arecord.png)

Po utworzeniu rekordu A uruchom `nslookup` do sprawdzania poprawności rozwiązuje rekordów.

![Wyszukiwanie dns publicznego adresu ip](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Usługa App Service (aplikacje sieci Web)

Poniżej przedstawiono krok po poprzez skonfigurowanie domeny niestandardowej do aplikacji sieci web usługi app service.

Przejdź do **usługi App Service** i wybierz zasób są Konfigurowanie niestandardowej nazwy domeny i kliknij przycisk **domen niestandardowych**.

Należy pamiętać, bieżący adres url na **domen niestandardowych** bloku, ten adres jest używany jako alias dla rekordu DNS utworzonego.

![Blok domen niestandardowych](./media/dns-custom-domain/url.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestaw rekordów**. Wypełnij następujące informacje w **Dodawanie zestawu rekordów** bloku i kliknij przycisk **OK** do jego utworzenia.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | mywebserver        | Ta wartość wraz z etykieta nazwy domeny jest nazwą FQDN dla nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME w systemie jest za pomocą aliasu. Jeśli zasób jest używany adres IP, będzie używany rekord.        |
|Czas wygaśnięcia     | 1        | 1 jest używana przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiaru czasu         |
|Alias     | webserver.azurewebsites.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS webserver.azurewebsites.net domyślne do aplikacji sieci web.        |


![Utwórz rekord CNAME](./media/dns-custom-domain/createcnamerecord.png)

Przejdź z powrotem do usługi app service, który jest skonfigurowany dla nazwy domeny niestandardowej. Kliknij przycisk **domen niestandardowych**, następnie kliknij przycisk **nazwy hostów**. Aby dodać rekord CNAME został utworzony, kliknij **+ Dodaj nazwę hosta**.

![rysunek 1](./media/dns-custom-domain/figure1.png)

Po zakończeniu procesu będą uruchamiane **nslookup** Aby sprawdzić rozpoznawanie nazw działa.

![rysunek 1](./media/dns-custom-domain/finalnslookup.png)

Aby dowiedzieć się więcej na temat mapowanie domeny niestandardowej w usłudze App Service, odwiedź stronę [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Jeśli musisz kupić domenę niestandardową, odwiedź stronę [kupowanie niestandardowej nazwy domeny dla usługi Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) Aby dowiedzieć się więcej na temat domen usługi App Service.

## <a name="blob-storage"></a>Blob Storage

Poniżej przedstawiono krok po poprzez Konfigurowanie rekordu CNAME dla konta usługi blob storage przy użyciu metody asverify. Ta metoda zapewnia, że nie ma żadnych przestojów.

Przejdź do **magazynu** > **kont magazynu**, a następnie wybierz swoje konto magazynu i kliknij przycisk **domena niestandardowa**. Zapisania, nazwy FQDN w kroku 2, ta wartość jest używana do utworzenia pierwszego rekordu CNAME

![domeny niestandardowej magazynu obiektów blob](./media/dns-custom-domain/blobcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestaw rekordów**. Wypełnij następujące informacje w **Dodawanie zestawu rekordów** bloku i kliknij przycisk **OK** do jego utworzenia.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | asverify.mystorageaccount        | Ta wartość wraz z etykieta nazwy domeny jest nazwą FQDN dla nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME w systemie jest za pomocą aliasu.        |
|Czas wygaśnięcia     | 1        | 1 jest używana przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiaru czasu         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS asverify.adatumfunctiona9ed.blob.core.windows.net domyślne konto magazynu.        |

Przejdź z powrotem do swojego konta magazynu, klikając **magazynu** > **kont magazynu**, wybierz konto magazynu i kliknij przycisk **domena niestandardowa**. Wpisz alias, utworzone bez prefiksu asverify w polu tekstowym wyboru ** Użyj pośredniej weryfikacji CNAME, a następnie kliknij przycisk **Zapisz**. Po wykonaniu tego kroku powrócić do strefy DNS, a następnie utworzyć rekord CNAME, bez prefiksu asverify.  Po tym punkcie, jest bezpiecznie usunąć rekord CNAME z prefiksem cdnverify.

![domeny niestandardowej magazynu obiektów blob](./media/dns-custom-domain/indirectvalidate.png)

Sprawdź poprawność rozpoznawania nazw DNS, uruchamiając `nslookup`

Aby dowiedzieć się więcej o mapowanie domeny niestandardowej do punktu końcowego magazynu obiektów blob, odwiedź stronę [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Usługa Azure CDN

Poniżej przedstawiono krok po poprzez Konfigurowanie rekordu CNAME dla punktu końcowego usługi CDN przy użyciu metody cdnverify. Ta metoda zapewnia, że nie ma żadnych przestojów.

Przejdź do **sieć** > **profilów CDN**, wybierz swój profil usługi CDN.

Wybierz punkt końcowy pracujesz z i kliknij przycisk **+ domena niestandardowa**. Uwaga **nazwę hosta punktu końcowego** ta wartość jest rekord, który wskazuje rekordu CNAME.

![Niestandardowa domena usługi CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij przycisk **+ zestaw rekordów**. Wypełnij następujące informacje w **Dodawanie zestawu rekordów** bloku i kliknij przycisk **OK** do jego utworzenia.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Name (Nazwa)     | cdnverify.mycdnendpoint        | Ta wartość wraz z etykieta nazwy domeny jest nazwą FQDN dla nazwy domeny niestandardowej.        |
|Typ     | CNAME        | Użyj rekord CNAME w systemie jest za pomocą aliasu.        |
|Czas wygaśnięcia     | 1        | 1 jest używana przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiaru czasu         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Nazwa DNS powoduje utworzenie aliasu, w tym przykładzie jest to nazwa DNS cdnverify.adatumcdnendpoint.azureedge.net domyślne konto magazynu.        |

Przejdź z powrotem do punktu końcowego usługi CDN, klikając **sieć** > **profilów CDN**i wybierz profil usługi CDN. Kliknij przycisk **+ domena niestandardowa** i wprowadź alias rekord CNAME, bez prefiksu cdnverify i kliknij przycisk **Dodaj**.

Po wykonaniu tego kroku powrócić do strefy DNS, a następnie utworzyć rekord CNAME, bez prefiksu cdnverify.  Po tym punkcie, jest bezpiecznie usunąć rekord CNAME z prefiksem cdnverify. Więcej informacji na temat usługi CDN i sposobie konfigurowania domenę niestandardową bez pośrednich rejestrację można znaleźć [zawartości mapy usługi Azure CDN na domenę niestandardową](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).
