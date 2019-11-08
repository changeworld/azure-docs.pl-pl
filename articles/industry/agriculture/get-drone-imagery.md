---
title: Pobieranie obrazów drona
description: Opisuje sposób pobierania obrazów drona z partnerów
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 598248a0efb3322a9c22a5e38e4986f5ba5142ab
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798455"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Pobieranie obrazów drona z partnerów drona

W tym artykule opisano, jak można przyorthomosaic dane z partnerów drona obrazów w systemie do usługi Azure FarmBeats Data Hub. Obecnie obsługiwane są następujące partnerzy obrazów:  

  ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrowanie danych drona obrazów za pomocą usługi Azure FarmBeats ułatwia uzyskiwanie danych orthomosaic z lotów drona prowadzonych w farmie w centrum danych. Po udostępnieniu danych można wyświetlić je w akceleratorze FarmBeats i można go użyć do kompilowania modeli danych i AI/ML.

## <a name="before-you-begin"></a>Przed rozpoczęciem

  - Upewnij się, że wdrożono usługę Azure FarmBeats. Aby wdrożyć, odwiedź stronę [Deploy FarmBeats](prepare-for-deployment.md).
  - Upewnij się, że masz farmę (dla której chcesz drona), która jest zdefiniowana w systemie FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Włącz integrację obrazów drona z usługą FarmBeats   

Aby włączyć integrację z usługą FarmBeats, należy podać następujące informacje dla dostawcy urządzenia:  
 - Punkt końcowy interfejsu API  
 - Identyfikator dzierżawy  
 - Identyfikator klienta  
 - Klucz tajny klienta  

Wykonaj następujące czynności:

1. Pobierz ten [skrypt](https://aka.ms/farmbeatspartnerscript) i wyodrębnij go na dysku lokalnym. W tym pliku ZIP znajdziesz dwa pliki.  
2. Zaloguj się do [Azure Portal](https://portal.azure.com/) i Otwórz Cloud Shell (Ta opcja jest dostępna w prawym górnym rogu portalu).   

    ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Upewnij się, że środowisko jest ustawione na program **PowerShell**

    ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Przekaż dwa pobrane pliki (z kroku 1 powyżej) do Cloud Shell.  

    ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Przejdź do katalogu, do którego pliki zostały przekazane. (Domyślnie zostanie ono przekazane do katalogu macierzystego > nazwa użytkownika).  
6. Uruchom następujący skrypt:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. Postępuj zgodnie z instrukcjami na ekranie, aby przechwycić wartości punktu końcowego interfejsu API, identyfikatora dzierżawy, identyfikatora klienta, klucza tajnego klienta i parametrów połączenia EventHub.

    Po wprowadzeniu wymaganych poświadczeń do systemu oprogramowania drona partnera będzie można zaimportować wszystkie farmy z systemu FarmBeats i użyć szczegółowych informacji o farmie w celu zaplanowania ścieżki lotu i zbierania obrazu drona.

    Po przetworzeniu nieprzetworzonych obrazów przez oprogramowanie dostawców drona oprogramowanie drona przekazuje załączone orthomosaic i inne przetworzone obrazy do centrum danych.

## <a name="view-drone-imagery"></a>Wyświetlanie obrazów drona

Po wysłaniu danych do centrum danych FarmBeats należy mieć możliwość wysyłania zapytań do magazynu scen przy użyciu interfejsów API usługi FarmBeats Data Hub.

Alternatywnie powinien być możliwe wyświetlenie najnowszego obrazu drona na stronie **szczegółów farmy** . Aby wyświetlić, wykonaj następujące czynności:  

1. Wybierz farmę, do której został przekazany obraz. Zostanie wyświetlona strona szczegóły **farmy** .
2. Przewiń w dół do sekcji Najnowsze **mapy dokładności** .
3. Obraz można wyświetlić w sekcji **obrazy drona** .

    ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Pobieranie obrazów drona

Po wybraniu sekcji drona obrazy zostanie otwarte okno podręczne pokazujące obraz o wysokiej rozdzielczości drona orthomosaic.

![Farmy projektów](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Wyświetl wszystkie mapy drona

Pliki i obrazy przekazane przez dostawcę drona są wyświetlane w sekcji Maps. Wybierz sekcję **Maps** , Filtruj według **farmy** i wybierz odpowiednie pliki do wyświetlenia i pobrania:

  ![Farmy projektów](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, w jaki sposób używać [interfejsów API](references-for-farmbeats.md#rest-api) FarmBeats Data Hub do pobierania obrazów drona.
