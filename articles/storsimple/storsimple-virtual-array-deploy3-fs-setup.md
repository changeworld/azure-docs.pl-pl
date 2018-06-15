---
title: Konfigurowanie wirtualnego tablicy StorSimple jako serwer plików | Dokumentacja firmy Microsoft
description: W tym samouczku trzeci w tablicy wirtualnego StorSimple wdrożenia powoduje, że możesz skonfigurować urządzenia wirtualnego jako serwer plików.
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
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927824"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Wdrożenie tablicy wirtualne StorSimple - zestawu się jako serwera plików za pośrednictwem portalu Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano sposób wykonywania początkowej konfiguracji, zarejestruj StorSimple serwera plików, skonfigurować urządzenie i utworzyć i łączyć się z udziałami SMB. Jest to ostatni artykuł z serii samouczki wdrażania wymaganych do całkowicie wdrożenia wirtualnego tablica jako serwera plików lub serwera iSCSI.

Instalacja i konfiguracja proces może zająć około 10 minut, aby zakończyć. Informacje przedstawione w tym artykule dotyczy tylko wdrożenia tablicy wirtualne StorSimple. Dla wdrożenia dla urządzeń z serii StorSimple 8000, przejdź do: [wdrażania urządzenia serii StorSimple 8000 z aktualizacją Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji
Przed skonfigurowaniem i konfigurowanie macierzy wirtualnego StorSimple, upewnij się, że:

* Udostępnione wirtualne tablicy i dołączone do niego zgodnie z opisem w [udostępnić tablicą wirtualnego StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [udostępnić tablicą wirtualnego StorSimple w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi Menedżera urządzeń StorSimple, utworzony w celu zarządzania tablicami wirtualne StorSimple. Aby uzyskać więcej informacji, zobacz [krok 2: pobieranie klucza rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla tablicy wirtualne StorSimple.
* Jeśli jest to drugi lub kolejny wirtualnego tablicy rejestrujesz istniejącą usługę Menedżer urządzeń StorSimple, powinien mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany podczas pierwszego urządzenia został pomyślnie zarejestrowany z tą usługą. W przypadku utraty tego klucza, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) dla macierzy wirtualne StorSimple.

## <a name="step-by-step-setup"></a>Krok po kroku instalacji
Poniższe instrukcje krok po kroku umożliwia instalowanie i konfigurowanie macierzy wirtualne StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Zakończenie ustawienia interfejsu użytkownika sieci web w lokalnej i rejestrowanie urządzenia
#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby zakończyć konfigurowanie i rejestrowanie urządzenia
1. Otwórz okno przeglądarki i nawiązać połączenia z lokalnego interfejsu użytkownika sieci web. Wpisz:
   
   `https://<ip-address of network interface>`
   
   Użyj adresu URL połączenia zauważono w poprzednim kroku. Zostanie wyświetlony komunikat o błędzie informujący, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Zaloguj się do sieci web UI macierzy wirtualnego jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia, który został zmodyfikowany w kroku 3: uruchomienie wirtualnych tablicy w [udostępnić tablicą wirtualnego StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub w [udostępnić tablicą wirtualnego StorSimple w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Zostają przeniesieni do **Home** strony. Na tej stronie opisano różne ustawienia wymagane do skonfigurowania, a rejestrowanie wirtualnego tablicy za pomocą usługi Menedżera urządzeń StorSimple. **Ustawień sieciowych**, **ustawienia serwera proxy w sieci Web**, i **ustawienia** są opcjonalne. Tylko ustawienia wymagane są **ustawienia urządzenia** i **ustawienia funkcji Cloud**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. W **ustawień sieciowych** w obszarze **interfejsy sieciowe**, dane 0 zostaną automatycznie skonfigurowane dla Ciebie. Każdego interfejsu sieciowego jest ustawiona domyślnie, aby uzyskać adres IP automatycznie (DHCP). W związku z tym adresem IP, podsieci i bramy są automatycznie przypisywane (dla protokołów IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Jeśli dodano więcej niż jeden interfejs sieciowy podczas inicjowania obsługi urządzenia, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejsu sieciowego można skonfigurować jako IPv4 tylko lub jako protokołów IPv4 i IPv6. Konfiguracje tylko protokół IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są one używane podczas prób urządzenia do komunikowania się z sieci dostawcy usług w chmurze magazynu lub do rozpoznania urządzenia według nazwy, gdy jest skonfigurowany jako serwer plików. W **ustawień sieciowych** w obszarze **serwerów DNS**:
   
   1. Podstawowego i pomocniczego serwera DNS są automatycznie konfigurowane. Jeśli użytkownik chce skonfigurować statyczne adresy IP, można określić serwery DNS. Wysokiej dostępności firma Microsoft zaleca, aby skonfigurować podstawowy i pomocniczy serwer DNS.
   2. Kliknij przycisk **Zastosuj** zastosowania i sprawdź poprawność ustawień sieciowych.
6. W **ustawienia urządzenia** strony:
   
   1. Przypisz unikatowy **nazwa** na urządzeniu. Ta nazwa może być 1 – 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij przycisk **serwera plików** ikona ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) dla **typu** urządzenia, które tworzysz. Serwer plików zostanie pozwala na tworzenie folderów udostępnionych.
   3. Urządzenie jest dostępna na serwerze plików, konieczne będzie dołączenie urządzenia do domeny. Wprowadź **nazwy domeny**.
   4. Kliknij przycisk **Zastosuj**.
7. Zostanie wyświetlone okno dialogowe. Wprowadź swoje poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru. Poświadczenia domeny są weryfikowane. Zostanie wyświetlony komunikat o błędzie, jeśli poświadczenia są nieprawidłowe.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kliknij przycisk **Zastosuj**. To spowoduje zastosowanie i sprawdź poprawność ustawień urządzenia.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Sprawdź, czy tablica wirtualnej jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i żadne obiekty zasad grupy (GPO) są stosowane do niego lub dziedziczone. Zasady grupy może zainstalować aplikacje, takie jak oprogramowanie antywirusowe na tablicy wirtualnego StorSimple. Instalowanie dodatkowego oprogramowania nie jest obsługiwana i może prowadzić do uszkodzenia danych. 
   > 
   > 
9. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalne, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   W **serwer proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *http://&lt;adres IP hosta lub FDQN&gt;: numer portu*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli przy użyciu uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowana.
10. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, na przykład serwery NTP podstawowych i pomocniczych i strefy czasowej. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia z dostawcy usług w chmurze.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    W **ustawienia** strony:
    
    1. Z listy rozwijanej wybierz **strefy czasowej** oparte na lokalizacji geograficznej, w którym jest wdrażany urządzenia. Domyślną strefę czasową dla Twojego urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **serwera NTP dodatkowej** dla danego urządzenia.
    4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i Zastosuj ustawienia w skonfigurowanym czasie.
11. Skonfiguruj ustawienia chmury dla danego urządzenia. W tym kroku zostanie Zakończ konfigurację urządzenia lokalnego, a następnie zarejestruj urządzenie usługi Menedżer StorSimple urządzenia.
    
    1. Wprowadź **klucz rejestracji usługi** pochodzący [krok 2: pobieranie klucza rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla tablicy wirtualne StorSimple.
    2. Jeśli jest to pierwszy urządzenia rejestrowania za pomocą tej usługi, użytkownik zobaczy **klucza szyfrowania danych usługi**. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. Ten klucz jest wymagany klucz rejestracji usługi, aby zarejestrować dodatkowych urządzeń w usłudze Menedżer StorSimple urządzenia. 
       
       Jeśli nie jest to pierwszy urządzenia, które w przypadku rejestracji z tą usługą, należy podać klucz szyfrowania danych usługi. Aby uzyskać więcej informacji, zapoznaj się pobrać [klucza szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) lokalnej interfejs użytkownika sieci web.
    3. Kliknij przycisk **zarejestrować**. Ponownie uruchomić urządzenie. Konieczne może być poczekaj 2 – 3 minutach urządzenie zostanie pomyślnie zarejestrowana. Po ponownym uruchomieniu urządzenia, nastąpi przekierowanie na stronę logowania.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Wróć do portalu Azure. Przejdź do **wszystkie zasoby**, wyszukaj usługi Menedżer StorSimple urządzenia.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Filtrowane listy, wybierz usługę Menedżer StorSimple urządzenia, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, sprawdź, czy urządzenie pomyślnie połączył się z usługą i ma stan **przejść do konfigurowania**.
    
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurowanie urządzenia jako serwer plików
Wykonaj poniższe kroki w [portalu Azure](https://portal.azure.com/) do ukończenia instalacji wymagane urządzenia.

#### <a name="to-configure-the-device-as-file-server"></a>Aby skonfigurować urządzenia jako serwera plików
1. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, wybierz urządzenie właśnie utworzony. To urządzenie będzie wyświetlany jako **przejść do konfigurowania**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kliknij urządzenie i będzie wyświetlany komunikat transparentu wskazujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kliknij przycisk **Konfiguruj** na pasku poleceń. Spowoduje to otwarcie **Konfiguruj** bloku. W **Konfiguruj** blok, wykonaj następujące czynności:
   
    1. Nazwa serwera plików jest wypełniane automatycznie.
    
    2. Upewnij się, że szyfrowanie magazynu w chmurze ma ustawioną wartość **włączone**. Spowoduje to zaszyfrować wszystkie dane, które są wysyłane do chmury. 
    
    3. Klucza AES 256-bitowego jest używany do szyfrowania kluczem zdefiniowane przez użytkownika. Określ klucz 32 znaków, a następnie ponownie klucz, aby je potwierdzić. Zarejestruj klucz w aplikacji zarządzania kluczami do użytku w przyszłości.
    
    4. Kliknij przycisk **Skonfiguruj wymagane ustawienia** Aby określić poświadczenia konta magazynu do użycia z urządzeniem. Kliknij przycisk **Dodaj nowe** Jeśli nie istnieją żadne poświadczenia konta magazynu skonfigurowane. **Upewnij się, że konto magazynu, że używasz obsługuje blokowe obiekty BLOB. Stronicowe obiekty BLOB nie są obsługiwane.** Więcej informacji na temat [blokuje obiekty BLOB i stronicowe obiekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. W **dodać poświadczeń konta magazynu** blok, wykonaj następujące czynności: 

    1. Wybierz subskrypcję, jeśli konto magazynu znajduje się w tej samej subskrypcji co usługa. Określ inne jest magazynem konto znajduje się poza subskrypcji usługi. 
    
    2. Z listy rozwijanej wybierz istniejące konto magazynu. 
    
    3. Lokalizacja zostanie wypełniona automatycznie na podstawie konta magazynu określony. 
    
    4. Włącz protokół SSL w celu zapewnienia bezpiecznej sieci kanał komunikacji między urządzeniem i chmurą.
    
    5. Kliknij przycisk **Dodaj** można dodać tego poświadczenia konta magazynu. 
   
        ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po pomyślnym utworzeniu poświadczeń konta magazynu, **Konfiguruj** bloku zostaną zaktualizowane, aby wyświetlić poświadczeń konta magazynu określony. Kliknij pozycję **Konfiguruj**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Zobaczysz, że plik jest tworzony serwera. Po pomyślnym utworzeniu serwera plików, otrzymasz powiadomienie.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stan urządzenia spowoduje również zmianę na **Online**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Można przystąpić do dodawania udziału.

## <a name="step-3-add-a-share"></a>Krok 3: Dodaj udział
Wykonaj poniższe kroki w [portalu Azure](https://portal.azure.com/) Aby utworzyć udział.

#### <a name="to-create-a-share"></a>Aby utworzyć udział
1. Wybierz urządzenie serwerze plików skonfigurowanym w poprzednim kroku, a następnie kliknij przycisk **...**  (lub kliknij prawym przyciskiem myszy). W menu kontekstowego wybierz **Dodaj udział**. Alternatywnie możesz kliknąć **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Określ następujące ustawienia udziału:

    1. Unikatowa nazwa udziału użytkownika. Nazwa musi być ciągiem o długości 3 do 127 znaków.
    
    2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziału.
    
    3. A **typu** udziału. Typem może być **warstwowego** lub **przypięty lokalnie**, z warstwy jest wartość domyślna. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie** udziału. Dla wszystkich innych danych wybierz **warstwowego** udziału.
    Udział przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane w udziale pozostaje na urządzeniu lokalnym i nie zostaną przeniesione do chmury. Udział warstwowy z drugiej strony jest alokowany elastycznie. Podczas tworzenia udziału warstwowych 10% miejsca jest inicjowana na warstwie lokalnej i 90% miejsca jest udostępniony w chmurze. Na przykład, jeśli udostępniane wolumin 1 TB, 100 GB, czy znajdują się w lokalnej przestrzeni i 900 GB byłoby używanych w chmurze po warstwy danych. Z kolei oznacza to, że jeśli całe lokalne miejsce na urządzeniu nie można dostarczać warstwowych udziału.
   
    4. W **ustawioną domyślną pełne uprawnienia** pola, przypisz uprawnienia użytkownika lub grupy, do której uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w  *john@contoso.com*  format. Zalecane jest użycie grupy użytkowników (zamiast jednego użytkownika) umożliwia uprawnień administratora uzyskać dostęp do tych udziałów. Po przypisaniu uprawnienia w tym miejscu, następnie służy Eksplorator plików do modyfikowania tych uprawnień.
   
    5. Kliknij przycisk **Dodaj** do utworzenia udziału. 
    
        ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Użytkownik jest powiadamiany, że trwa tworzenie udziału.
   
        ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    Po utworzeniu udziału z określonymi ustawieniami **udziałów** bloku zostanie zaktualizowana, aby odzwierciedlić nowego udziału. Domyślnie monitorowania i kopii zapasowej są włączone dla udziału.
   
    ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Łączenie się z udziałem
Należy teraz nawiązać połączenia z co najmniej jeden udział, które zostały utworzone w poprzednim kroku. Wykonaj następujące kroki na hoście z systemem Windows Server podłączonym do sieci wirtualne StorSimple.

#### <a name="to-connect-to-the-share"></a>Aby nawiązać połączenie z udziałem
1. Naciśnij klawisz ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. W oknie Uruchamianie określ *&#92; &#92;&lt; Nazwa serwera plików&gt;*  jako ścieżkę, zastępując *nazwy serwera plików* o nazwie urządzenia przypisane do serwera plików. Kliknij przycisk **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Spowoduje to otwarcie zapasowej Eksploratora plików. Teraz należy można zobaczyć udziałów, które zostały utworzone jako foldery. Wybierz, a następnie kliknij dwukrotnie udziału (folder) w celu wyświetlenia zawartości.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Można teraz dodać pliki do te udziały i wykonaj kopię zapasową.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak używać lokalnej sieci web interfejsu użytkownika Służącego do [administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

