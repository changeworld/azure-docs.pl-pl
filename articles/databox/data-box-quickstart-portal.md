---
title: 'Szybki start: usługa Microsoft Azure Data Box | Microsoft Docs'
description: Dowiedz się, jak szybko wdrożyć usługę Azure Data Box w witrynie Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: alkohli
ms.openlocfilehash: 21e784320bc2b0785cdd28354947c0df6c35daf4
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207994"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-using-the-azure-portal"></a>Szybki start: wdrażanie usługi Azure Data Box za pomocą witryny Azure Portal

::: zone-end

::: zone target="chromeless"

# <a name="get-started-with-azure-data-box"></a>Wprowadzenie do Azure Data Box 

::: zone-end

::: zone target="docs"

W tym przewodniku Szybki start opisano sposób wdrażania usługi Azure Data Box przy użyciu witryny Azure Portal. Przedstawiono między innymi czynności podłączania i konfigurowania urządzenia Data Box oraz kopiowania na nie danych do przekazania na platformę Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-ordered.md)

::: zone-end 

::: zone target="chromeless"

W tym przewodniku opisano sposób wdrażania Azure Data Box przy użyciu Azure Portal. Kroki obejmują przeglądanie wymagań wstępnych, kabli i połączenia urządzenia oraz kopiowanie danych do urządzenia w celu przekazywania ich do platformy Azure.

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). 

- Sprawdź, czy masz dostęp na poziomie właściciela lub współautora do subskrypcji, aby móc utworzyć zamówienie urządzenia Data Box.
- Zapoznaj się z [zasadami bezpieczeństwa dotyczącymi urządzenia Data Box](data-box-safety.md).
- Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 
- Należy mieć dostęp do płaskiej powierzchni, na której można umieścić urządzenie Data Box. Jeśli chcesz umieścić urządzenie na standardowej półce na stojaku, na stojaku centrum danych potrzebne jest gniazdo 7U. Urządzenie możesz umieścić na stojaku płasko lub pionowo.
- Należy zapewnić następujące kable w celu podłączenia urządzenia Data Box do komputera hosta.
    - Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2)
    - Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym MGMT)
    - Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 GB/s lub 1 GB/s)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. [Ukończono samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-ordered.md).
2. Otrzymano urządzenie Data Box i zostanie **dostarczony**stan zamówienia w portalu. 
3. Przegląd [wytycznych dotyczących bezpieczeństwa urządzenie Data Box](data-box-safety.md).
4. Odebrano jeden uziemiony przewód zasilający używany z urządzeniem magazynującym 100 TB.
5. Dostęp do komputera-hosta zawierającego dane, które mają zostać skopiowane do urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 
6. Dostęp do płaskiej powierzchni w celu umieszczenia urządzenie Data Box. Aby urządzenie było płaskie lub pionowe w standardowym półce stojaka, potrzebne jest gniazdo 7U w stojaku.
7. Pozyskasz następujące kable, aby połączyć urządzenie Data Box z komputerem hosta.
    - Co najmniej jeden kabel miedziany SFP+ Twinax 10 GbE lub kable światłowodowe SFP+ (do użytku z interfejsami sieciowymi DATA 1 i DATA 2). Aby uzyskać więcej informacji, zobacz [listę obsługiwanych kabli i przełączników z karty Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf) , które są zgodne z kartami mellanox ConnectX®-3 Pro pl Dual-port 10GBase-T z interfejsem sieciowym PCI Express 3,0.
    - Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym MGMT)
    - Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 GB/s lub 1 GB/s)

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box**. Maksymalna dostępna do użycia pojemność tego rozwiązania to 80 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.



## <a name="cable"></a>Podłączenie 

Ten krok zajmuje około 10 minut.

Po otrzymaniu urządzenia Data Box wykonaj następujące czynności w celu podłączenia przewodów, nawiązania połączenia i uruchomienia urządzenia. Ten krok zajmuje około 10 minut.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przed podłączeniem przewodów do urządzenia sprawdź, czy masz następujące przewody:
    
    - (Dołączony) Przewód zasilający z uziemieniem przeznaczony do prądu co najmniej 10 A, ze złączem typu IEC60320 C-13 po jednej stronie, umożliwiającym podłączenie do urządzenia.
    - Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym MGMT)
    - Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2 o przepustowości 10 Gb/s)
    - Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 GB/s lub 1 GB/s)

3. Wyjmij urządzenie i umieść je na płaskiej powierzchni. 
    
4. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Płyta montażowa urządzenia Data Box z podłączonymi kablami](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Podłącz kabel zasilający do urządzenia.
    2. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu zarządzania (MGMT) na urządzeniu. 
    3. Użyj kabla miedzianego SFP+ Twinax, aby podłączyć co najmniej jeden interfejs sieciowy 10 Gb/s (wersja preferowana, ale możliwe również użycie interfejsu 1 Gb/s), DATA 1 lub DATA 2, do przesyłu danych. 
    4. Włącz urządzenie. Przycisk zasilania znajduje się na panelu przednim urządzenia.


## <a name="connect"></a>Połącz

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne > Szczegóły urządzenia** w witrynie [Azure Portal](https://portal.azure.com).
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze używanym do nawiązania połączenia z urządzeniem Data Box. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia. 
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie, informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślne ustawienie sieci dla interfejsu 10 Gb/s (lub 1 Gb/s) to DHCP. Jeśli jest taka potrzeba, można skonfigurować interfejs jako statyczny i podać adres IP. 

## <a name="copy-data"></a>Kopiuj dane

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych i prędkości połączenia sieciowego.
 
1. Jeśli używasz hosta z systemem Windows, skorzystaj z narzędzia do kopiowania plików zgodnego z protokołem SMB, na przykład Robocopy. W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`. Połącz narzędzie z urządzeniem i rozpocznij kopiowanie danych do udziałów. Aby uzyskać więcej informacji na temat kopiowania danych za pomocą narzędzia Robocopy, zobacz [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
2. Połącz się z udziałami przy użyciu następującej ścieżki: `\\<IP address of your device>\ShareName`. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.
3. Upewnij się, że nazwy udziałów, folderów i danych są zgodne z instrukcjami przedstawionymi w temacie [Azure Storage and Data Box service limits (Ograniczenia usług Azure Storage i Data Box)](data-box-limits.md).

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure 

Ta operacja trwa około 10–15 minut.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania. 
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia. 
3. Zwrotna etykieta wysyłkowa powinna być widoczna na wyświetlaczu E-ink. Jeśli etykieta nie jest widoczna na wyświetlaczu E-ink, pobierz etykietę wysyłkową z witryny Azure Portal i umieść ją w przezroczystej koszulce znajdującej się na urządzeniu.
4. Zamknij opakowanie i wyślij do firmy Microsoft. 

## <a name="verify-data"></a>Weryfikacja danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure, automatycznie rozpocznie się przekazywanie danych na platformę Azure. 
2. Usługa Azure Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal. 

    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

- Możesz anulować zamówienie urządzenia Data Box w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start wdrożono usługę Azure Data Box w celu zaimportowania danych na platformę Azure. Aby dowiedzieć się więcej na temat zarządzania usługą Azure Data Box, przejdź do kolejnego samouczka: 

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box (Administrowanie usługą Data Box w witrynie Azure Portal)](data-box-portal-admin.md)

::: zone-end


