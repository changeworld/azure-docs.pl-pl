---
title: Pobieranie obrazów z dronów
description: W tym artykule opisano, jak uzyskać zdjęcia dronów od partnerów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132051"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Uzyskaj zdjęcia dronów od partnerów dronów

W tym artykule opisano, jak można wnieść dane ortomozowe z partnerów obrazów drone do usługi Azure FarmBeats Datahub. Ortozaj jest ilustracją lotniczą lub obrazem, który jest geometrycznie poprawiony i zszyty na podstawie danych zebranych przez drona.

Obecnie obsługiwane są następujące obrazy partnerów.

  ![FarmBeats drone imagery partners FarmBeats drone imagery partners FarmBeats drone imagery partners FarmBeat](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integracja danych z obrazów dronów z usługą Azure FarmBeats pomaga uzyskać dane ortozaiczne z lotów dronów przeprowadzanych w farmie do datahub. Po udostępnieniu danych można je wyświetlić w akceleratorze FarmBeats. Dane mogą być używane do fuzji danych i sztucznej inteligencji oraz tworzenia modelu uczenia maszynowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

  - Upewnij się, że zainstalowano usługę Azure FarmBeats. Aby uzyskać informacje na temat instalowania farmbeats, zobacz [Instalowanie platformy Azure FarmBeats](install-azure-farmbeats.md).
  - Upewnij się, że masz farmę, dla której chcesz obrazowania drone zdefiniowane w systemie FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Włącz integrację zdjęć dronów z FarmBeats

Podaj dostawcom urządzeń następujące informacje, aby umożliwić integrację z FarmBeats:
 - Punkt końcowy interfejsu API
 - Identyfikator dzierżawy
 - Identyfikator klienta
 - Klucz tajny klienta

Wykonaj następujące kroki.

1. Pobierz ten [skrypt](https://aka.ms/farmbeatspartnerscript)i wyodrębnij go na dysk lokalny. Dwa pliki znajdują się wewnątrz pliku zip.
2. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) i otwórz usługę Azure Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu portalu.

    ![Otwórz usługę Azure Cloud Shell na prawym górnym pasku portalu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Upewnij się, że środowisko jest ustawione na **program PowerShell**.

    ![Ustawienie programu PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Przekaż dwa pliki pobrane z kroku 1 w wystąpieniu usługi Cloud Shell.

    ![Przekazywanie plików](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Przejdź do katalogu, w którym zostały przesłane pliki. Domyślnie są one przekazywane do katalogu macierzystego pod nazwą użytkownika.
6. Uruchom następujący skrypt:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby przechwycić wartości punktu końcowego interfejsu API, identyfikatora dzierżawy, identyfikatora klienta, klucza tajnego klienta i ciągu połączenia EventHub.

    Po wprowadzeniu wymaganych poświadczeń do systemu oprogramowania drona partnera można zaimportować wszystkie farmy z systemu FarmBeats. Następnie możesz użyć szczegółów farmy, aby zaplanować tor lotu i kolekcję obrazów dronów.

    Po przetworzeniu surowych obrazów przez oprogramowanie dostawców dronów, system oprogramowania dronów przesyła do datahuba zszyte obrazy ortomozaiczne i inne przetworzone obrazy.

## <a name="view-drone-imagery"></a>Zobacz zdjęcia z drona

Po wysłaniu danych do pola danych FarmBeats można zbadać magazyn scen przy użyciu interfejsów API pola DANYCH FarmBeats.

Możesz też wyświetlić najnowszy obraz drona na stronie **Szczegóły farmy.** Aby wyświetlić obraz, wykonaj następujące czynności.

1. Wybierz farmę, do której zostały przesłane zdjęcia. Zostanie **wyświetlona** strona Szczegóły farmy.
2. Przewiń w dół do najnowszej sekcji **Mapy precyzyjne.**
3. Zobacz obraz w sekcji **Obrazy dronów.**

    ![Sekcja Zdjęć dronów](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Pobierz zdjęcia z drona

Po wybraniu sekcji Obrazy dronów otwiera się okno podręczne, aby wyświetlić obraz ortomozaii w wysokiej rozdzielczości.

![Ortomozai o wysokiej rozdzielczości](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Zobacz wszystkie mapy dronów

Pliki i obrazy przesłane przez dostawcę drona są wyświetlane w sekcji **Mapy.** Wybierz sekcję **Mapy,** **filtruj**według farmy i wybierz odpowiednie pliki do wyświetlenia i pobrania.

  ![Sekcja Mapy](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z [interfejsów API](rest-api-in-azure-farmbeats.md) FarmBeats Datahub w celu uzyskania zdjęć dronów.
