---
title: Konfigurowanie rozwiązania StorSimple Virtual Array jako serwera plików | Dokumentacja firmy Microsoft
description: To trzeci samouczek z wdrażania rozwiązania StorSimple Virtual Array powoduje, że można skonfigurować urządzenia wirtualnego jako serwer plików.
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
ms.openlocfilehash: a931b303e40e41bc23e8b586e1d37e600625b1a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61415240"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Wdrażanie rozwiązania StorSimple Virtual Array — zestaw się jako serwer plików za pośrednictwem witryny Azure portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano sposób wykonywania początkowej konfiguracji, zarejestrować swojego serwera plików usługi StorSimple, przeprowadzić konfigurację urządzenia i tworzyć i łączyć się z udziałami SMB. Jest to ostatnia artykuł z tej serii samouczków wdrożenia wymagane całkowicie wdrożenie macierz wirtualna jako serwer plików lub serwera iSCSI.

Proces instalacji i konfiguracji może potrwać około 10 minut, aby zakończyć. Informacje przedstawione w tym artykule dotyczy tylko wdrożenia rozwiązania StorSimple Virtual Array. Wdrożenia urządzeń StorSimple serii 8000 przejdź do: [Wdrażanie urządzenia StorSimple 8000 series z aktualizacją Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji
Przed skonfigurowaniem i konfigurowanie rozwiązania StorSimple Virtual Array, upewnij się, że:

* Masz obsługiwana macierz wirtualną i dołączone do niego zgodnie z opisem w [Aprowizowanie macierzy wirtualnej StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [Aprowizowanie macierzy wirtualnej StorSimple w środowisku VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługą Menedżera urządzeń StorSimple, utworzony w celu zarządzania macierze wirtualne StorSimple. Aby uzyskać więcej informacji, zobacz [krok 2: Pobierz klucz rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla rozwiązania StorSimple Virtual Array.
* Jeśli jest to drugi lub kolejny macierzy wirtualnej, w przypadku rejestracji za pomocą istniejącej usługi Menedżer urządzeń StorSimple, powinny mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany podczas pierwszego urządzenia został pomyślnie zarejestrowany z tą usługą. W przypadku utraty tego klucza, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) dla rozwiązania StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Instrukcje krok po kroku instalacji
Poniższe instrukcje umożliwiają instalowanie i konfigurowanie rozwiązania StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Ukończ konfigurację lokalnego internetowego interfejsu użytkownika i rejestrowanie urządzenia
#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby ukończyć instalację i rejestrowanie urządzenia
1. Otwórz okno przeglądarki i nawiązać połączenie z lokalnego Interfejsu w przeglądarce. Wpisz:
   
   `https://<ip-address of network interface>`
   
   Użyj adresu URL połączenia zanotowanego w poprzednim kroku. Zostanie wyświetlony komunikat o błędzie informujący, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Zaloguj się do internetowego interfejsu użytkownika macierz wirtualna jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia, który został zmodyfikowany w kroku 3: Uruchom macierz wirtualną [Aprowizowanie macierzy wirtualnej StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [Aprowizowanie macierzy wirtualnej StorSimple w środowisku VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Nastąpi przekierowanie do **Home** strony. Ta strona w tym artykule opisano różne ustawienia wymagane do skonfigurowania i zarejestruj macierz wirtualną w usłudze Menedżer urządzeń StorSimple. **Ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia urządzenia** i **ustawienia chmury**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. W **ustawienia sieciowe** strony w obszarze **interfejsy sieciowe**, interfejs DATA 0 zostaną automatycznie skonfigurowane dla Ciebie. Każdy interfejs sieciowy jest ustawiana domyślnie, aby uzyskać adres IP automatycznie (DHCP). Dlatego adres IP, podsieci i bramę są automatycznie przypisywane (dla protokołów IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Jeśli więcej niż jeden interfejs sieciowy jest dodawany podczas aprowizowania urządzenia, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejsu sieciowego można skonfigurować jako IPv4 tylko lub jako IPv4 i IPv6. Tylko konfiguracje protokołu IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są one używane, gdy urządzenie próbuje nawiązać połączenia z dostawców usługi magazynu w chmurze lub rozwiązać urządzenia według nazwy, gdy jest skonfigurowany jako serwer plików. W **ustawienia sieciowe** strony w obszarze **serwerów DNS**:
   
   1. Serwer DNS podstawowe i pomocnicze są konfigurowane automatycznie. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, można określić serwery DNS. Wysoką dostępność zaleca się skonfigurowanie podstawowy i pomocniczy serwer DNS.
   2. Kliknij przycisk **Zastosuj** do Zastosuj i zweryfikuj ustawienia sieciowe.
6. W **ustawienia urządzenia** strony:
   
   1. Przypisz unikatowy **nazwa** do Twojego urządzenia. Ta nazwa może zawierać 1 – 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij przycisk **serwera plików** ikonę ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) dla **typu** urządzenia, które tworzysz. Serwer plików umożliwia tworzenie folderów udostępnionych.
   3. Ponieważ urządzenie jest serwerem plików, konieczne będzie dołączenie urządzenia do domeny. Wprowadź **nazwy domeny**.
   4. Kliknij przycisk **Zastosuj**.
7. Pojawi się okno dialogowe. Wprowadź swoje poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru. Poświadczenia domeny są weryfikowane. Zobaczysz komunikat o błędzie, jeśli poświadczenia są nieprawidłowe.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień urządzenia.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory, a nie obiekty zasad grupy (GPO) są stosowane do niego lub dziedziczone. Zasady grupy mogą instalować aplikacje, takie jak oprogramowanie antywirusowe na macierz wirtualną StorSimple. Instalowanie dodatkowego oprogramowania nie jest obsługiwana i może prowadzić do uszkodzenia danych. 
   > 
   > 
9. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalny, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   W **serwera proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *http://&lt;adres IP hosta lub nazwa FQDN&gt;: numer portu*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli przy użyciu uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany.
10. (Opcjonalnie) skonfiguruj ustawienia czasu dla danego urządzenia, takie jak strefę czasową i podstawowych i pomocniczych serwerów NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    W **ustawienia czasu** strony:
    
    1. Z listy rozwijanej wybierz **strefa czasowa** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **serwera NTP dodatkowej** dla Twojego urządzenia.
    4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosować ustawienia czasu skonfigurowane.
11. Skonfiguruj ustawienia chmury dla Twojego urządzenia. W tym kroku możesz ukończyć konfiguracji lokalnego urządzenia i zarejestrować urządzenie w usłudze Menedżer urządzeń StorSimple.
    
    1. Wprowadź **klucz rejestracji usługi** uzyskany [krok 2: Pobierz klucz rejestracji usługi](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) dla rozwiązania StorSimple Virtual Array.
    2. Jeśli jest to pierwsze urządzenie rejestrowane w tej usłudze, użytkownik zobaczy **klucza szyfrowania danych usługi**. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. Ten klucz jest wymagany przy użyciu klucza rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze Menedżer urządzeń StorSimple. 
       
       Jeśli nie jest to pierwsze urządzenie, które są rejestrowane w tej usłudze, musisz podać klucz szyfrowania danych usługi. Aby uzyskać więcej informacji, zapoznaj się uzyskać [klucza szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) lokalnej interfejs użytkownika sieci web.
    3. Kliknij przycisk **zarejestrować**. To spowoduje ponowne uruchomienie urządzenia. Może być konieczne poczekaj 2-3 minut, zanim urządzenie zostanie pomyślnie zarejestrowana. Po ponownym uruchomieniu urządzenia, nastąpi przekierowanie do strony logowania.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Wróć do witryny Azure Portal. Przejdź do **wszystkie zasoby**, wyszukaj usługi Menedżer urządzeń StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na wyświetlonej wyfiltrowanej liście wybierz swoją usługę Menedżer urządzeń StorSimple, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, sprawdź, czy urządzenie pomyślnie nawiązało połączenie usługi i ma stan **gotowe do skonfigurowania**.
    
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurowanie urządzenia jako serwer plików
Wykonaj poniższe kroki w [witryny Azure portal](https://portal.azure.com/) do ukończenia instalacji wymagane urządzenia.

#### <a name="to-configure-the-device-as-file-server"></a>Aby skonfigurować urządzenia jako serwer plików
1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku wybierz urządzenia został właśnie utworzony. To urządzenie będzie wyświetlany jako **gotowe do skonfigurowania**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Kliknij urządzenie, a następnie zostanie wyświetlony komunikat z transparent informujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Kliknij przycisk **Konfiguruj** na pasku poleceń. Spowoduje to otwarcie **Konfiguruj** bloku. W **Konfiguruj** blok, wykonaj następujące czynności:
   
   1. Nazwa serwera plików jest wypełniane automatycznie.
    
   2. Upewnij się, że szyfrowanie magazynu w chmurze jest ustawiona na **włączone**. To spowoduje zaszyfrowanie wszystkich danych, które są wysyłane do chmury. 
    
   3. 256-bitowego klucza AES jest używany do szyfrowania kluczem zdefiniowanych przez użytkownika. Należy określić klucz o długości 32 znaków, a następnie wprowadź ponownie klucz, aby je potwierdzić. Rejestruje klucz w aplikacji do zarządzania kluczami do użytku w przyszłości.
    
   4. Kliknij przycisk **Skonfiguruj wymagane ustawienia** Aby określić poświadczenia konta magazynu do użycia z Twoim urządzeniem. Kliknij przycisk **Dodaj nowe** Jeśli nie istnieją żadne poświadczenia konta magazynu, skonfigurowane. **Upewnij się, że konto magazynu, że używasz obsługuje blokowe obiekty BLOB. Stronicowe obiekty BLOB nie są obsługiwane.** Więcej informacji na temat [blokuje i stronicowe obiekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. W **Dodawanie poświadczeń konta magazynu** blok, wykonaj następujące czynności: 

    1. Wybierz bieżącą subskrypcję, jeśli konto magazynu jest w tej samej subskrypcji co usługa. Określ inne jest magazynem konto znajduje się poza subskrypcją usługi. 
    
    2. Z listy rozwijanej wybierz istniejące konto magazynu. 
    
    3. Lokalizacja zostanie wypełniona automatycznie oparte na określone konto magazynu. 
    
    4. Włącz protokół SSL w celu zapewnienia kanału bezpiecznej komunikacji sieciowej między urządzeniem i chmurą.
    
    5. Kliknij przycisk **Dodaj** do dodania tych poświadczeń konta magazynu. 
   
        ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po pomyślnym utworzeniu poświadczenia konta magazynu, **Konfiguruj** blok zostanie zaktualizowana w celu wyświetlenia podane poświadczenia konta magazynu. Kliknij pozycję **Konfiguruj**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Będzie informacja, że plik jest tworzony serwer. Po pomyślnym utworzeniu serwera plików, otrzymasz powiadomienie.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Jego stan zmieni się również na **Online**.
   
   ![Konfigurowanie serwera plików](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Możesz przejść, aby dodać udział.

## <a name="step-3-add-a-share"></a>Krok 3: Dodawanie udziału
Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/), aby utworzyć udział.

#### <a name="to-create-a-share"></a>Aby utworzyć udział
1. Wybierz urządzenie serwer plików skonfigurowany w poprzednim kroku, a następnie kliknij przycisk **...**  (lub kliknij prawym przyciskiem myszy). W menu kontekstowym wybierz **Dodaj udział**. Alternatywnie możesz kliknąć **+ Dodaj udział** na pasku poleceń urządzenia.
   
   ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Określ następujące ustawienia udziału:

   1. Unikatowa nazwa udziału. Nazwa musi być ciąg, który zawiera 3 do 127 znaków.
    
   2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziału.
    
   3. A **typu** udziału. Typ może być **warstwowy** lub **przypięty lokalnie**, przy użyciu warstwowego domyślna. Dla obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie** udostępniania. Dla wszystkich innych danych wybierz **warstwowy** udostępniania.
      Udział przypiętego lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane w udziale pozostaje lokalne na urządzeniu i nie zostaną przeniesione do chmury. Udziału warstwowego z drugiej strony jest alokowany elastycznie. Po utworzeniu udziału warstwowego 10% wolnego miejsca jest inicjowana na warstwie lokalnej i 90% miejsca są aprowizowane w chmurze. Na przykład, jeśli aprowizowano 1 TB woluminu 100 GB będzie znajdować się w lokalnej przestrzeni i 900 GB będzie używana w chmurze podczas warstwy danych. Z kolei oznacza to, że jeśli zabraknie całe lokalne miejsce na urządzeniu nie można dostarczać udziału warstwowego.
   
   4. W **Ustaw domyślne pełne uprawnienia** pola, należy przypisać uprawnienia użytkownika lub grupy, który uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w *john\@contoso.com* formatu. Zalecamy użycie grupy użytkowników (zamiast jednego użytkownika) umożliwia uprawnień administratora dostępu do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
   
   5. Kliknij przycisk **Dodaj** Aby utworzyć udział. 
    
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału.
   
       ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Po utworzeniu udziału z określonymi ustawieniami **udziałów** blok zostanie zaktualizowana w celu odzwierciedlenia nowego udziału. Domyślnie monitorowanie i tworzenie kopii zapasowych są włączone dla udziału.
   
      ![Dodawanie udziału](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Łączenie z udziałem
Teraz należy połączyć się z co najmniej jeden udział, które zostały utworzone w poprzednim kroku. Wykonaj następujące czynności na hoście z systemem Windows Server połączyć rozwiązania StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Aby nawiązać połączenie z udziałem
1. Naciśnij klawisz ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. W oknie Uruchamianie określ *&#92; &#92; &lt;nazwy serwera plików&gt;* jako ścieżkę, zastępując *nazwy serwera plików* z nazwa urządzenia, która została przypisana do pliku serwer. Kliknij przycisk **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Spowoduje to otwarcie Eksploratora plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. Wybierz i kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Możesz teraz dodać pliki do tych udziałów i wykonaj kopię zapasową.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak używać lokalnego internetowego interfejsu użytkownika do [administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

