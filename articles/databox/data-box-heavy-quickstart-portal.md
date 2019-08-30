---
title: Przewodnik Szybki Start dotyczący Data Box Heavy Microsoft Azure | Microsoft Docs
description: Dowiedz się, jak szybko wdrożyć Azure Data Box Heavy w Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 433c3ac08d8d7d8158d4cc958979782e3b3192e1
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164420"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Szybki start: Wdrażanie Azure Data Box Heavy przy użyciu Azure Portal

W tym przewodniku szybki start opisano sposób wdrażania Azure Data Box Heavy przy użyciu Azure Portal. Kroki te zawierają informacje dotyczące sposobu kabli, konfigurowania i kopiowania danych do Data Box Heavy tak, aby zostały przekazane do platformy Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: Azure Data Box Heavy kolejności](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji lokacji, usługi urządzenie Data Box i urządzenia.

### <a name="for-installation-site"></a>Lokacja instalacji

Przed rozpoczęciem upewnij się, że:

- Urządzenie może być zgodne ze wszystkimi entryways. Wymiary urządzenia: Szerokość: 26 "Długość: 48 "Wysokość: 28 ".
- Użytkownik ma dostęp do urządzenia za pośrednictwem windy lub rampy, jeśli planujesz zainstalować ją w piętrzie innym niż piętro podłogowe.
- Masz dwie osoby do obsługi urządzenia. Urządzenie odważy około 500 funtów. i zawiera koła.
- W centrum danych znajduje się płaska lokacja z bliskością dostępnego połączenia sieciowego, które może obsłużyć urządzenie z tym ustawieniem.

### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:

- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Subskrypcja używana na potrzeby usługi urządzenie Data Box to [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Masz właściciela lub dostęp współautora do subskrypcji, aby utworzyć zamówienie Data Box Heavy.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:

- Zawarto przegląd [wytycznych dotyczących bezpieczeństwa Data Box Heavy](data-box-safety.md).
- Masz komputer hosta połączony z siecią centrum danych. Data Box Heavy skopiuje dane z tego komputera. Na komputerze hosta musi być uruchomiony [obsługiwany system operacyjny](data-box-heavy-system-requirements.md).
- Istnieje laptop z kablem RJ-45 do łączenia się z lokalnym interfejsem użytkownika i konfigurowania urządzenia. Aby skonfigurować każdy węzeł urządzenia, użyj laptopa.
- Twoje centrum danych ma sieć o dużej szybkości i masz co najmniej 1 10 GbE połączeń.
- Potrzebny jest kabel 1 40-GB/s lub kabel 10 GB/s dla każdego węzła urządzenia. Wybierz kable, które są zgodne z interfejsem sieciowym Mellanox MCX314A-BCCT:
    - W przypadku kabla 40-GB/s urządzenie musi mieć wartość QSFP +.
    - W przypadku kabla 10 GB/s potrzebny jest kabel SFP +, który jest podłączany do 10-gramowego przełącznika na jednym końcu, przy użyciu karty QSFP + do SFP + (lub karty QSA) dla punktu końcowego, który jest podłączony do urządzenia.
- Kable zasilające są dołączone do zasobnika z tyłu urządzenia.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz **Data Box Heavy**. Maksymalna użyteczna pojemność to 770 TB i można utworzyć wiele zamówień dla większych rozmiarów danych.
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kabel i połączenie z urządzeniem

Po przejrzeniu wymagań wstępnych można nawiązać połączenie z urządzeniem i połączyć się z nim.

::: zone-end

## <a name="cable-for-power"></a>Kabel do podłączenia

Ten krok zajmie około 5 minut.

Po otrzymaniu Data Box Heavy wykonaj następujące kroki, aby podłączyć urządzenie do urządzenia i włączyć urządzenie.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przenieś urządzenie do lokacji instalacji i Zablokuj tylne koła.
3. Podłącz wszystkie cztery kable zasilające do zasilaczy z tyłu urządzenia.
4. Aby włączyć węzły urządzeń, użyj przycisków zasilanych na płaszczyźnie czołowej.

## <a name="cable-first-node-for-network"></a>Pierwszy węzeł kabla dla sieci

Wykonanie tego kroku zajmie około 10-15 minut.

1. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu zarządzania (MGMT) na urządzeniu.
2. Użyj kabla Twinax QSFP + miedź, aby podłączyć co najmniej 1 40 GB/s (preferowany ponad 1 GB/s) interfejs sieciowy, dane 1 lub dane 2 dla danych. W przypadku korzystania z przełącznika 10 GB/s należy użyć kabla Twinax SFP + miedzianego z adapterem QSFP + do SFP + (karta QSA) w celu połączenia z interfejsem sieciowym 40 GB dla danych.
3. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Data Box Heavy okablowanie](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Skonfiguruj pierwszy węzeł

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne > Szczegóły urządzenia** w witrynie [Azure Portal](https://portal.azure.com). To samo hasło jest używane w przypadku obu węzłów urządzenia.
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze używanym do łączenia się z Data Box Heavy. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia.
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie, informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślnie ustawienia sieci dla interfejsów (z wyłączeniem zarządzania) są skonfigurowane jako DHCP. W razie konieczności można skonfigurować te interfejsy jako statyczne i podać adres IP.

## <a name="cable-and-configure-the-second-node"></a>Podłącz drugi węzeł i skonfiguruj go

Wykonanie tego kroku zajmie około 15-20 minut.

Postępuj zgodnie z instrukcjami dotyczącymi pierwszego węzła, aby podłączyć kabel i skonfigurować drugi węzeł na urządzeniu.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopiuj dane

Czas do ukończenia tej operacji zależy od rozmiaru danych i szybkości sieci, do której są kopiowane dane.
 
1. Kopiuj dane do obu węzłów urządzenia jednocześnie, jednocześnie używając interfejsów danych 40 GB/s.

    - W przypadku korzystania z hosta z systemem Windows należy użyć narzędzia do kopiowania plików zgodnego z protokołem SMB, takiego jak [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`.
2. Nawiąż połączenie z udziałami na urządzeniu przy użyciu ścieżki`\\<IP address of your device>\ShareName`:. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **połącz & Kopiuj** w lokalnym interfejsie użytkownika sieci Web Data Box Heavy.
3. Upewnij się, że nazwy udziału i folderu oraz dane są zgodne z wskazówkami opisanymi w [limitach usługi Azure Storage i Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Przygotowywanie do wysłania

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Po zakończeniu kopiowania danych bez żadnych błędów przejdź do strony **przygotowanie do wysłania** w lokalnym interfejsie użytkownika sieci Web i Rozpocznij przygotowywanie.
2. Po pomyślnym zakończeniu **przygotowanie do wysłania** na obu węzłach Wyłącz urządzenie z poziomu lokalnego interfejsu użytkownika sieci Web.

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Wykonanie tej operacji trwa około 15-20 minut.

1. Usuń kable i przywróć je do zasobnika z tyłu urządzenia.
2. Zaplanuj pobranie w ramach operatora regionalnego.
3. Uzyskaj dostęp do [urządzenie Data Box operacji](mailto:DataBoxOps@microsoft.com) , aby informować o odbiorze i uzyskać etykietę wysyłki zwrotnej.
4. Etykieta wysyłania zwrotnego powinna być widoczna w panelu przedniego na urządzeniu.

## <a name="verify-data"></a>Weryfikacja danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box Heavy jest połączone z siecią centrum danych Azure, dane zostaną automatycznie przekazane do platformy Azure.
2. Usługa urządzenie Data Box powiadamia o ukończeniu kopiowania danych za pośrednictwem Azure Portal.

    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

- Możesz anulować kolejność Data Box Heavy w Azure Portal przed przetworzeniem zamówienia. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Data Box Heavy, aby pomóc w zaimportowaniu danych do platformy Azure. Aby dowiedzieć się więcej o zarządzaniu Azure Data Box Heavy, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Użyj Azure Portal do administrowania Data Box Heavy](data-box-portal-admin.md)

::: zone-end
