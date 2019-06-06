---
title: Przewodnik Szybki start dotyczący platformy Microsoft Azure Data Box ciężkich | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szybko wdrożyć usługi Azure Data pole duże w witrynie Azure portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 930da7367b3f5d7e20617afedef007efea97c51a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518582"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Szybki start: Wdrażanie usługi Azure Data pole mocno przy użyciu witryny Azure portal

Ten przewodnik Szybki Start opisano sposób wdrażania usługi Azure Data pole mocno przy użyciu witryny Azure portal. Kroki obejmują jak okablowanie, konfigurowanie i kopiowanie danych do duże pole danych, dzięki czemu przekazuje go do platformy Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: Duże pole danych na platformie Azure zamówienia](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia, należy wykonać następujące wymagania wstępne dotyczące konfiguracji dla instalacji lokacji, usługa Data Box i urządzenia.

### <a name="for-installation-site"></a>Dla instalacji lokacji

Przed rozpoczęciem upewnij się, że:

- Urządzenie może składać się za pośrednictwem wszystkie swoje entryways. Wymiary urządzenia są: szerokość: 26" długość: 48" height: 28”.
- Masz dostęp do urządzenia za pośrednictwem elevator lub zwiększenia, jeśli planujesz zainstalować piętra niż dostęp.
- Masz dwie osoby do obsługi urządzenia. Urządzenie waga wynosi około ~ 500 modułów równoważenia obciążenia. przypada na koła.
- Masz prostych witryn w centrum danych za pomocą odległości połączenie dostępnej sieci, która może pomieścić urządzenia przy użyciu ten system.

### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:

- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Subskrypcja używane dla usługi Data Box jest [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview), lub [dostępu Sponsorowanego Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Masz dostęp właściciela lub współautora do subskrypcji, aby utworzyć zamówienie duże pole danych.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:

- Po przejrzeniu [zaleceń dla Twojej duże pole danych](data-box-safety.md).
- Masz komputerze hosta podłączonym do sieci centrum danych. Duże pole danych umożliwia skopiowanie danych z tego komputera. Należy uruchomić komputer-host [obsługiwanym systemie operacyjnym](data-box-heavy-system-requirements.md).
- Użytkownik ma komputer przenośny za pomocą kabla RJ-45, aby nawiązać połączenie z lokalnego interfejsu użytkownika i skonfigurować urządzenie. Aby skonfigurować jeden raz każdego węzła urządzenia przy użyciu komputera przenośnego.
- Centrum danych ma o dużej szybkości sieci i masz co najmniej 10 GbE połączenia.
- Wymagany jest jeden kabel 40-GB/s lub kabel 10 GB/s na każdy węzeł urządzenia. Wybierz kable, które są zgodne z Mellanox MCX314A-BCCT interfejs sieciowy:
    - Kabel 40-GB/s urządzenia koniec kabla musi być QSFP +.
    - Kabel 10 GB/s wymaga kabel SFP + podłączony do przełącznik 10 G, na jednym końcu QSFP + SFP + karty (lub kartą QSA) dla elementu end, które podłącza się do urządzenia.
- Przewodów zasilania znajdują się w zasobniku tyłu urządzenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz **Data Box ciężkich**. Maksymalnej pojemności do wykorzystania 770 TB, a można utworzyć wiele zamówień w przypadku większych ilości danych.
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.

## <a name="cable-for-power"></a>Kabel zasilania

Ten krok zajmuje około 5 minut.

Po otrzymaniu duże pole danych, wykonaj następujące czynności Podłączanie urządzenia zasilania i Włącz urządzenie.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przenieść urządzenia do instalacji lokacji i Zablokuj tylne koła.
3. Wszystkie kable zasilania cztery nawiązać połączenie zasilacze tyłu urządzenia.
4. Użyj przycisków zasilania na płaszczyźnie frontonu, aby włączyć w węzłach urządzenia.

## <a name="cable-first-node-for-network"></a>Kabel pierwszego węzła sieci

W tym kroku trwa około 10 – 15 minut.

1. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu zarządzania (MGMT) na urządzeniu.
2. Użyj kabla Twinax QSFP + miedzianego można podłączyć co najmniej jeden 40 GB/s (preferowane ponad 1 GB/s) interfejsu sieciowego, DATA 1 lub 2 danych dla danych. Jeśli używasz przełącznika 10 GB/s, za pomocą kabla miedzianego Twinax SFP + QSFP + SFP + karty (karta QSA) do łączenia z interfejsu sieciowego 40-GB/s dla danych.
3. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Rozpakowane duże pole danych](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Skonfigurować pierwszy węzeł

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne > Szczegóły urządzenia** w witrynie [Azure Portal](http://portal.azure.com). Dla obu węzłów urządzenia używane to samo hasło.
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze, którego używasz połączyć się z dużymi pola danych. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia.
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie, informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślnie ustawienia sieci dla interfejsów (z wyjątkiem MGMT) są skonfigurowane jako DHCP. W razie potrzeby można skonfigurować te interfejsy jako statyczny i podaj adres IP.

## <a name="cable-and-configure-the-second-node"></a>Podłącz i skonfiguruj drugi węzeł

W tym kroku trwa około 15 – 20 minut.

Postępuj zgodnie z krokami dla pierwszego węzła Podłącz i skonfiguruj drugi węzeł na urządzeniu.  

## <a name="copy-data"></a>Kopiowanie danych

Czas wymagany do ukończenia tej operacji zależy od tego, że rozmiar danych i prędkości sieci, w którym dane są kopiowane.
 
1. Kopiuj dane do obu węzłów urządzenia przy użyciu zarówno 40-GB/s danych interfejsy równolegle.

    - Jeśli przy użyciu hosta Windows używać takich jak narzędzia do kopiowania plików zgodnych SMB [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`.
2. Łączyć się z udziałami na urządzeniu przy użyciu ścieżki:`\\<IP address of your device>\ShareName`. Aby uzyskać poświadczenia dostępu do udziału, przejdź do **Connect & copy** strony w lokalnym internetowym interfejsie użytkownika duże pole danych.
3. Upewnij się, że udział i nazwy folderu oraz dane postępuj zgodnie z wytycznymi opisany w [limity dla usługi Azure Storage i duże pole danych](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Po kopiowanie danych zostanie zakończone bez żadnych błędów, przejdź do **przygotowywanie do wysłania** strony w lokalnym internetowym interfejsie użytkownika, a następnie uruchom przygotowanie wysyłki.
2. Po **przygotowywanie do wysłania** została ukończona pomyślnie na obu węzłach, wyłącz funkcję urządzenia, z poziomu lokalnego internetowego interfejsu użytkownika.

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Ta operacja trwa około 15 – 20 minut.

1. Usuń kable i przywrócić je do zasobnika tyłu urządzenia.
2. Za pomocą operatora regionalnej, należy zaplanować odbioru.
3. Dotrzyj do [operacje na danych pole](mailto:DataBoxOps@microsoft.com) informuje o odbiór i Pobierz zwrotną etykietę wysyłkową.
4. Etykiety wysyłki zwrotnej powinny być widoczne na panelu przednim wyczyść urządzenia.

## <a name="verify-data"></a>Weryfikacja danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy duże pole dane urządzenie jest podłączone do sieci centrum danych platformy Azure, danych jest automatyczne przekazywanie na platformie Azure.
2. Usługa Data Box powiadamia o ukończeniu kopiowanie danych za pośrednictwem witryny Azure portal.

    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

- Duże pole danych kolejność, w witrynie Azure portal można anulować, przed przetworzeniem kolejności. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono dane pole mocno ułatwia importowanie danych do platformy Azure. Aby dowiedzieć się więcej o zarządzaniu dużymi pole danych platformy Azure, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Używanie witryny Azure portal do administrowania duże pole danych](data-box-portal-admin.md)
