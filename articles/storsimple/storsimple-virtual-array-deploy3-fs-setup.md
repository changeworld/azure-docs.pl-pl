---
title: Konfigurowanie tablicy wirtualnej StorSimple jako serwera plików | Dokumenty firmy Microsoft
description: Ten trzeci samouczek we wdrożeniu tablicy wirtualnej StorSimple nakazuje skonfigurowanie urządzenia wirtualnego jako serwera plików.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297641"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Wdrażanie tablicy wirtualnej StorSimple — konfigurowanie jako serwer plików za pośrednictwem witryny Azure portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym artykule opisano sposób przeprowadzania wstępnej konfiguracji, rejestrowania serwera plików StorSimple, ukończenia konfiguracji urządzenia oraz tworzenia udziałów SMB i łączenia się z nim. Jest to ostatni artykuł z serii samouczków wdrażania wymaganych do całkowitego wdrożenia tablicy wirtualnej jako serwera plików lub serwera iSCSI.

Proces konfiguracji i konfiguracji może potrwać około 10 minut. Informacje zawarte w tym artykule dotyczą tylko wdrożenia tablicy wirtualnej StorSimple. Aby wdrożyć urządzenia z serii StorSimple 8000, przejdź do: [Wdrażanie urządzenia z serii StorSimple 8000 z aktualizacją 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji
Przed skonfigurowaniem i skonfigurowaniem tablicy wirtualnej StorSimple upewnij się, że:

* Aprowizacja tablicy wirtualnej i połączenie z nią, jak opisano w [aprowizowania StorSimple Virtual Array w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [Provision a StorSimple Virtual Array w VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi StorSimple Device Manager, który został utworzony w celu zarządzania StorSimple tablicwirtua. Aby uzyskać więcej informacji, zobacz [krok 2: Pobierz klucz rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla StorSimple Virtual Array.
* Jeśli jest to druga lub kolejna tablica wirtualna, która rejestrujesz się w istniejącej usłudze StorSimple Device Manager, powinien mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany, gdy pierwsze urządzenie zostało pomyślnie zarejestrowane w tej usłudze. Jeśli ten klucz został utracony, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) dla tablicy wirtualnej StorSimple.

## <a name="step-by-step-setup"></a>Konfiguracja krok po kroku
Skorzystaj z poniższych instrukcji krok po kroku, aby skonfigurować i skonfigurować tablicę wirtualną StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Ukończ konfigurację lokalnego interfejsu użytkownika sieci Web i zarejestruj urządzenie
#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby zakończyć konfigurację i zarejestrować urządzenie
1. Otwórz okno przeglądarki i połącz się z lokalnym interfejsem użytkownika sieci Web. Wpisz:
   
   `https://<ip-address of network interface>`
   
   Użyj adresu URL połączenia odnotowanego w poprzednim kroku. Zostanie wyświetlony błąd wskazujący, że wystąpił problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij **przycisk Kontynuuj do tej strony sieci Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Zaloguj się do internetowego interfejsu użytkownika tablicy wirtualnej jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia zmienione w kroku 3: Uruchom [tablicę wirtualną w programie Aprowizuj tablicę wirtualną StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub w [programie Aprowizjuj tablicę wirtualną StorSimple w VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Zostaniesz przesuń do strony **głównej.** Na tej stronie opisano różne ustawienia wymagane do skonfigurowania i zarejestrowania tablicy wirtualnej w usłudze StorSimple Device Manager. **Ustawienia sieciowe,** **Ustawienia serwera proxy sieci Web**i Ustawienia **czasu** są opcjonalne. Jedynymi wymaganymi **ustawieniami** są ustawienia urządzenia i **ustawienia chmury**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na stronie **Ustawienia sieci** w obszarze **Interfejsy sieci**dane 0 zostaną automatycznie skonfigurowane. Każdy interfejs sieciowy jest domyślnie ustawiany tak, aby automatycznie uzyskać adres IP (DHCP). W związku z tym adres IP, podsieć i brama są automatycznie przypisywane (zarówno dla Protokołu IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Jeśli dodano więcej niż jeden interfejs sieciowy podczas inicjowania obsługi administracyjnej urządzenia, można skonfigurować je w tym miejscu. Należy zauważyć, że można skonfigurować interfejs sieciowy tylko jako IPv4 lub zarówno jako IPv4 i IPv6. Konfiguracje tylko IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są używane, gdy urządzenie próbuje komunikować się z dostawcami usług magazynu w chmurze lub rozpoznać urządzenie według nazwy, gdy jest skonfigurowane jako serwer plików. Na stronie **Ustawienia sieci** pod **serwerami DNS**:
   
   1. Podstawowy i pomocniczy serwer DNS są konfigurowane automatycznie. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, można określić serwery DNS. Aby uzyskać wysoką dostępność, zaleca się skonfigurowanie podstawowego i pomocniczego serwera DNS.
   2. Kliknij **przycisk Zastosuj,** aby zastosować i sprawdzić poprawność ustawień sieciowych.
6. Na stronie **Ustawienia urządzenia:**
   
   1. Przypisz unikatową **nazwę** do urządzenia. Ta nazwa może zawierać od 1 do 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij **File server** ikonę ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) Serwer plików dla **typu** urządzenia, które tworzysz. Serwer plików umożliwia tworzenie folderów udostępnionych.
   3. Ponieważ urządzenie jest serwerem plików, musisz dołączyć urządzenie do domeny. Wprowadź **nazwę domeny**.
   4. Kliknij przycisk **Zastosuj**.
7. Pojawi się okno dialogowe. Wprowadź poświadczenia domeny w określonym formacie. Kliknij ikonę wyboru. Poświadczenia domeny są weryfikowane. Jeśli poświadczenia są niepoprawne, zostanie wyświetlony komunikat o błędzie.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień urządzenia.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Active Directory i nie są stosowane ani dziedziczone żadne obiekty zasad grupy.Ensure that your virtual array is in its own organizational unit (OU) for Active Directory and no group policy objects (GPO) are applied to it or inherited. Zasady grupy mogą instalować aplikacje, takie jak oprogramowanie antywirusowe, w tablicy wirtualnej StorSimple. Instalowanie dodatkowego oprogramowania nie jest obsługiwane i może prowadzić do uszkodzenia danych. 
   > 
   > 
9. (Opcjonalnie) skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na stronie **serwera proxy sieci Web:**
   
   1. Podaj **adres URL serwera proxy sieci Web** w tym formacie: http:// adres *&lt;hosta-IP lub numer :Port FQDN&gt;*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelnianie** jako **podstawowe** lub **brak**.
   3. W przypadku korzystania z uwierzytelniania należy również podać **nazwę użytkownika** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to sprawdzenie poprawności i zastosowanie skonfigurowanych ustawień serwera proxy sieci Web.
10. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, takie jak strefa czasowa oraz podstawowe i pomocnicze serwery NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na stronie **Ustawienia czasu:**
    
    1. Z listy rozwijanej wybierz **strefę czasową** na podstawie lokalizacji geograficznej, w której urządzenie jest wdrażane. Domyślną strefą czasową urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj domyślną wartość time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie określ **pomocniczy serwer NTP** dla urządzenia.
    4. Kliknij przycisk **Zastosuj**. Spowoduje to sprawdzenie poprawności i zastosowanie skonfigurowanych ustawień czasu.
11. Skonfiguruj ustawienia chmury dla swojego urządzenia. W tym kroku zostanie zakończona konfiguracja urządzenia lokalnego, a następnie zarejestrujesz urządzenie w usłudze StorSimple Device Manager.
    
    1. Wprowadź **klucz rejestracji usługi,** który został wprowadzony w [kroku 2: Pobierz klucz rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla tablicy wirtualnej StorSimple.
    2. Jeśli jest to twoje pierwsze urządzenie rejestrujące się w tej usłudze, zostanie wyświetlony **klucz szyfrowania danych usługi.** Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. Ten klucz jest wymagany przy przy tym kluczu rejestracji usługi do rejestrowania dodatkowych urządzeń w usłudze StorSimple Device Manager. 
       
       Jeśli nie jest to pierwsze urządzenie, które rejestrujesz się w tej usłudze, musisz podać klucz szyfrowania danych usługi. Aby uzyskać więcej informacji, zobacz, aby uzyskać [klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) w lokalnym interfejsie użytkownika sieci Web.
    3. Kliknij **pozycję Zarejestruj**. Spowoduje to ponowne uruchomienie urządzenia. Przed pomyślnym zarejestrowaniem urządzenia może być konieczne odczekanie 2-3 minut. Po ponownym uruchomieniu urządzenia zostaniesz przesunięty na stronę logowania.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Wróć do witryny Azure Portal. Przejdź do **aplikacji Wszystkie zasoby**— wyszukaj usługę Menedżer urządzeń StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na liście filtrowanych wybierz usługę StorSimple Device Manager, a następnie przejdź do **pozycji Zarządzanie > urządzeniami**. W **bloku Urządzenia** sprawdź, czy urządzenie pomyślnie połączyło się z usługą i ma stan **Gotowy do skonfigurowania**.
    
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurowanie urządzenia jako serwera plików
Wykonaj następujące kroki w witrynie [Azure portal,](https://portal.azure.com/) aby ukończyć wymaganą konfigurację urządzenia.

#### <a name="to-configure-the-device-as-file-server"></a>Aby skonfigurować urządzenie jako serwer plików
1. Przejdź do usługi StorSimple Device Manager, a następnie przejdź do **witryny Zarządzanie > urządzeniami**. W bloku **Urządzenia** wybierz właśnie utworzone urządzenie. To urządzenie będzie wyświetlane jako **Gotowe do skonfigurowania**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kliknij urządzenie, a zobaczysz komunikat banera informujący, że urządzenie jest gotowe do konfiguracji.
   
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kliknij **pozycję Konfiguruj** na pasku poleceń. Spowoduje to otwarcie **bloku Konfiguruj.** W bloku **Konfiguruj** wykonaj następujące czynności:
   
   1. Nazwa serwera plików jest automatycznie wypełniana.
    
   2. Upewnij się, że szyfrowanie magazynu w chmurze jest ustawione na **Włączone**. Spowoduje to zaszyfrowanie wszystkich danych, które są wysyłane do chmury. 
    
   3. 256-bitowy klucz AES jest używany z kluczem zdefiniowanym przez użytkownika do szyfrowania. Określ klucz 32 znaków, a następnie ponownie wejdź do klucza, aby go potwierdzić. Zapisz klucz w aplikacji do zarządzania kluczami do wykorzystania w przyszłości.
    
   4. Kliknij **pozycję Konfiguruj wymagane ustawienia,** aby określić poświadczenia konta magazynu, które mają być używane z urządzeniem. Kliknij **przycisk Dodaj nowy,** jeśli nie skonfigurowano żadnych poświadczeń konta magazynu. **Upewnij się, że używane konto magazynu obsługuje blokowe obiekty blob. Obiekty BLOB strony nie są obsługiwane.** Więcej informacji o [blokach obiektów blob i stronicowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. W bloku **Dodawanie poświadczeń konta magazynu** wykonaj następujące czynności: 

    1. Wybierz bieżącą subskrypcję, jeśli konto magazynu znajduje się w tej samej subskrypcji co usługa. Określ inne jest konto magazynu znajduje się poza subskrypcją usługi. 
    
    2. Z listy rozwijanej wybierz istniejące konto magazynu. 
    
    3. Lokalizacja zostanie automatycznie wypełniona na podstawie określonego konta magazynu. 
    
    4. Włącz funkcję TLS, aby zapewnić bezpieczny kanał komunikacji sieciowej między urządzeniem a chmurą.
    
    5. Kliknij **przycisk Dodaj,** aby dodać poświadczenia konta magazynu. 
   
        ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po pomyślnym utworzeniu poświadczenia konta magazynu blok **Konfiguruj** zostanie zaktualizowany w celu wyświetlenia poświadczeń określonego konta magazynu. Kliknij **pozycję Konfiguruj**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Zostanie wyświetlony, że serwer plików jest tworzony. Po pomyślnym utworzeniu serwera plików zostaniesz o tym powiadomiony.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stan urządzenia zmieni się również na **Online**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Możesz przystąpić do dodawania udziału.

## <a name="step-3-add-a-share"></a>Krok 3: Dodaj udział
Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/), aby utworzyć udział.

#### <a name="to-create-a-share"></a>Aby utworzyć udział
1. Wybierz urządzenie serwera plików skonfigurowane w poprzednim kroku i kliknij **...** (lub kliknij prawym przyciskiem myszy). W menu kontekstowym wybierz polecenie **Dodaj udział**. Alternatywnie można kliknąć **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Określ następujące ustawienia udziału:

   1. Unikatowa nazwa twojego udziału. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
    
   2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziałów.
    
   3. Typ **Type** dla udziału. Typ może być **warstwowy** lub **przypięty lokalnie,** przy czym poziomowe jest wartością domyślną. W przypadku obciążeń, które wymagają gwarancji lokalnych, niskich opóźnień i wyższej wydajności, wybierz udział **przypięty lokalnie.** W przypadku wszystkich innych danych wybierz udział **warstwowy.**
      Lokalnie przypięty udział jest aprowizowana i zapewnia, że podstawowe dane w udziale pozostają lokalne na urządzeniu i nie wyciekają do chmury. Udział warstwowy z drugiej strony jest cienko aprowizacji. Podczas tworzenia udziału warstwowego 10% miejsca jest aprowizowana w warstwie lokalnej, a 90% miejsca jest aprowizowana w chmurze. Na przykład jeśli aprowizowana wolumin 1 TB, 100 GB będzie znajdować się w przestrzeni lokalnej i 900 GB będą używane w chmurze, gdy warstwy danych. To z kolei oznacza, że jeśli zabraknie całego miejsca lokalnego na urządzeniu, nie można aprowizować udział warstwowy.
   
   4. W polu **Ustaw domyślne pełne uprawnienia do** przypisania uprawnień do użytkownika lub grupy uzyskującej dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w formacie *\@Johna contoso.com.* Zaleca się użycie grupy użytkowników (zamiast jednego użytkownika), aby zezwolić uprawnienia administratorowi na dostęp do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
   
   5. Kliknij **przycisk Dodaj,** aby utworzyć udział. 
    
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału.
   
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Po utworzeniu udziału z określonymi ustawieniami, **Shares** bloku zostanie zaktualizowany, aby odzwierciedlić nowy udział. Domyślnie monitorowanie i tworzenie kopii zapasowych są włączone dla udziału.
   
      ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Połącz się z udziałem
Teraz musisz połączyć się z co najmniej jednym udziałem utworzonym w poprzednim kroku. Wykonaj te czynności na hoście systemu Windows Server połączonym z tablicą wirtualną StorSimple.

#### <a name="to-connect-to-the-share"></a>Aby połączyć się z udziałem
1. Naciśnij ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. W oknie Uruchom określ * &lt;&gt; nazwę&#92;&#92;serwera plików* jako ścieżkę, zastępując nazwę serwera *plików* nazwą urządzenia przypisaną do serwera plików. Kliknij przycisk **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Spowoduje to otwarcie Eksploratora plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. Wybierz i kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Teraz możesz dodawać pliki do tych udziałów i wykonać kopię zapasową.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zarządzać [tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md)za pomocą lokalnego interfejsu użytkownika sieci Web.

