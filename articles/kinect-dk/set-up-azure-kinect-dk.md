---
title: Szybki start — konfigurowanie zestawu Azure Kinect DK
description: Ten przewodnik Szybki start zawiera instrukcje dotyczące konfigurowania sprzętu zestawu Azure Kinect DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211282"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Szybki start: Konfigurowanie zestawu Azure Kinect DK

Ten przewodnik Szybki start zawiera wskazówki dotyczące sposobu konfigurowania zestawu Azure Kinect DK. Pokażemy, jak przetestować wizualizację strumienia czujnika i korzystać z narzędzia [Azure Kinect Viewer](azure-kinect-viewer.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="system-requirements"></a>Wymagania systemowe

Sprawdź [wymagania systemowe](system-requirements.md), aby sprawdzić, czy konfiguracja komputera hosta spełnia wszystkie minimalne wymagania zestawu Azure Kinect DK.

## <a name="set-up-hardware"></a>Konfigurowanie sprzętu

> [!NOTE]
> Przed rozpoczęciem korzystania z urządzenia usuń folię zabezpieczającą kamery.

1. Włóż wtyk przewodu zasilającego do gniazda zasilania z tyłu urządzenia. Podłącz zasilacz USB do drugiego końca kabla, a następnie podłącz zasilacz do gniazda sieciowego.
2. Podłącz kabel danych USB do urządzenia, a następnie do portu USB 3.0 w komputerze.
   >[!NOTE]
   >Aby uzyskać najlepsze wyniki, podłącz kabel bezpośrednio do urządzenia i do komputera. Należy unikać stosowania przedłużaczy lub dodatkowych adapterów w połączeniu.

3. Sprawdź, czy dioda LED wskaźnika zasilania (obok kabla USB) świeci ciągłym białym światłem.
  
   Załączanie zasilania urządzenia trwa kilka sekund. Urządzenie jest gotowe do użycia, gdy znajdujący się z przodu urządzenia wskaźnik LED przesyłania strumieniowego się wyłączy.  

   Aby uzyskać więcej informacji na temat diody LED wskaźnika zasilania, zapoznaj się z sekcją [Co oznacza stan lampki wskaźnika zasilania?](hardware-specification.md#what-does-the-light-mean)

    ![Pełne funkcje urządzenia](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Pobieranie zestawu SDK

1. Wybierz link, aby [pobrać zestaw SDK](sensor-sdk-download.md).
2. Zainstaluj zestaw SDK na komputerze.

## <a name="update-firmware"></a>Aktualizowanie oprogramowania układowego

Do poprawnego działania zestaw SDK wymaga najnowszej wersji oprogramowania układowego urządzenia. Aby sprawdzić i zaktualizować wersję oprogramowania układowego, wykonaj kroki opisane w artykule [Aktualizowanie oprogramowania układowego zestawu Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Sprawdzanie, czy urządzenie przesyła strumieniowo dane

1. Uruchom narzędzie [Azure Kinect Viewer](azure-kinect-viewer.md). Narzędzie to można uruchomić za pomocą jednej z następujących metod:
   - Możesz uruchomić narzędzie viewer z poziomu wiersza polecenia lub przez dwukrotne kliknięcie pliku wykonywalnego. Plik wykonywalny `k4aviewer.exe` znajduje się w katalogu narzędzi zestawu SDK (na przykład `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, gdzie `X.Y.Z` jest zainstalowaną wersją zestawu SDK).
   - Możesz uruchomić program Azure Kinect Viewer z menu **Start** urządzenia.
2. W programie Azure Kinect Viewer wybierz pozycję **Open Device** > **Start** (Otwórz urządzenie > Start).

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Sprawdź, czy narzędzie wizualizuje strumień z każdego czujnika:
   - Kamera z pomiarem głębi
   - Kamera kolorowa
   - Kamera na podczerwień
   - Moduł IMU
   - Mikrofony

    ![Narzędzie do wizualizacji](./media/quickstarts/visualization-tool.png)

Konfiguracja zestawu Azure Kinect DK została ukończona. Teraz możesz rozpocząć tworzenie aplikacji lub integrowanie usług.

Jeśli masz jakieś problemy, zobacz [Rozwiązywanie problemów](troubleshooting.md).

## <a name="see-also"></a>Zobacz też

- [Informacje o sprzęcie zestawu Azure Kinect DK](hardware-specification.md)
- [Aktualizowanie oprogramowania układowego urządzenia](update-device-firmware.md)
- Dowiedz się więcej o programie [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Następne kroki

Kiedy zestaw Azure Kinect DK jest gotowy i działa, możesz również zapoznać się z samouczkiem
> [!div class="nextstepaction"]
> [Rejestrowanie strumieni z czujników w pliku](record-sensor-streams-file.md)
