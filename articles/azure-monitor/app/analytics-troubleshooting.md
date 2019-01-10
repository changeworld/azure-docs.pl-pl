---
title: Rozwiązywanie problemów z analizą w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: 'Problemy z usługą Application Insights analytics? Zacznij tutaj. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: bf999e15c04a64f717bcd7bac87e357d5ccc92e6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189936"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Rozwiązywanie problemów z analizą w usłudze Application Insights
Problemy z [analizy usługi Application Insights](analytics.md)? Zacznij tutaj. Analytics to zaawansowane wyszukiwanie narzędzia usługi Azure Application Insights.

## <a name="limits"></a>Limity
* Obecnie wyniki zapytania są ograniczone do dokładnie w ciągu tygodnia ostatnich danych.
* Przetestowanie w przeglądarkach: najnowszej wersji programu Chrome, Microsoft Edge i Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Rozszerzenia znanych niezgodna przeglądarka
* Ghostery

Wyłącz rozszerzenia, lub użyj innej przeglądarki.

## <a name="e-a"></a> "Nieoczekiwany błąd"
![Nieoczekiwany błąd ekranu](media/analytics-troubleshooting/010.png)

Wystąpił błąd wewnętrzny podczas portalu środowiska uruchomieniowego nieobsługiwany wyjątek.

* Wyczyść pamięć podręczną przeglądarki. 

## <a name="e-b"></a>403 ... Spróbuj załadować ponownie
![403 ... Spróbuj załadować ponownie](media/analytics-troubleshooting/020.png)

Wystąpił błąd (podczas uwierzytelniania lub podczas generowania tokenu dostępu) związany z uwierzytelnianiem. Portal może nie mieć możliwości odzyskiwania bez zmiany ustawień przeglądarki.

* Sprawdź [pliki cookie innych firm są włączone](#cookies) w przeglądarce. 

## <a name="authentication"></a>403 ... Sprawdź strefy zabezpieczeń
![403 ...podjęciem ponownej próby upewnij strefy zabezpieczeń](media/analytics-troubleshooting/030.png)

Wystąpił błąd (podczas uwierzytelniania lub podczas generowania tokenu dostępu) związany z uwierzytelnianiem. Portal może nie mieć możliwości odzyskiwania bez zmiany ustawień przeglądarki.

1. Sprawdź [pliki cookie innych firm są włączone](#cookies) w przeglądarce. 
2. Czy do otwarcia portalu użyto ulubionych, zakładki lub zapisanego linku? Czy zalogowano się przy użyciu innych poświadczeń niż użyte podczas zapisywania linku?
3. Spróbuj użyć okna przeglądarki w trybie prywatnym/incognito (po zamknięciu wszystkich tych okien). Musisz podać swoje poświadczenia. 
4. Otwórz (inny zwykłe) okno przeglądarki i przejdź do [Azure](https://portal.azure.com). Wyloguj się. Następnie otwórz hiperlink i zaloguj się z prawidłowymi poświadczeniami.
5. Microsoft Edge i przeglądarki Internet Explorer, użytkownicy mogą również otrzymać ten błąd, gdy ustawienia zaufanej strefy nie są obsługiwane.
   
    Sprawdź zarówno [portalu analiza](https://portal.azure.com) i [portalu Azure Active Directory](https://portal.azure.com) znajdują się w tej samej strefie zabezpieczeń:
   
   * W programie Internet Explorer Otwórz **Opcje internetowe**, **zabezpieczeń**, **Zaufane witryny**, **witryn**:
     
     ![Okno dialogowe Opcje internetowe, dodanie lokacji do zaufanych witryn](media/analytics-troubleshooting/033.png)
     
     Na liście witryn internetowych, jeśli uwzględnione są którekolwiek z następujących adresów URL, upewnij się, że uwzględniono również pozostałe:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Nie znaleziono zasobu
![404 ... nie można odnaleźć zasobu](media/analytics-troubleshooting/040.png)

Zasób aplikacji został usunięty z usługi Application Insights i t nie jest dostępna już. Może to nastąpić, jeśli adres URL został zapisany do strony usługi Analytics.

## <a name="e-e"></a>403 ... Brak autoryzacji
![403 ... nieautoryzowane](media/analytics-troubleshooting/050.png)

Nie masz uprawnień do otwarcia tej aplikacji w usłudze Analytics.

* Czy został wyświetlony link od kogoś innego? Poproś go, aby upewnić się, że jesteś w [czytelnicy lub współautorzy dla tej grupy zasobów](../../azure-monitor/app/resources-roles-access-control.md).
* Czy zapisać link przy użyciu innych poświadczeń? Otwórz [witryny Azure portal](https://portal.azure.com), wyloguj się, a następnie spróbuj ten link ponownie, podając poprawne poświadczenia.

## <a name="html-storage"></a>403 ... Magazyn HTML5
Nasz portal używa HTML5 localStorage i sessionStorage.

* Dla programu Chrome: Ustawienia prywatności, ustawienia zawartości.
* Program Internet Explorer: Opcje internetowe, karta Zaawansowane zabezpieczenia, Włącz magazyn modelu DOM

![403 ... Spróbuj włączyć magazyn HTML5](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Nie znaleziono subskrypcji
![404 ... Nie znaleziono subskrypcji](media/analytics-troubleshooting/070.png)

Adres URL jest nieprawidłowy. 

* Otwórz zasób aplikacji w [portalu Application Insights](https://portal.azure.com). Następnie użyj przycisku analizy.

## <a name="e-h"></a>404 ... strona nie istnieje.
![404 ... Strona nie istnieje](media/analytics-troubleshooting/080.png)

Adres URL jest nieprawidłowy.

* Otwórz zasób aplikacji w [portalu Application Insights](https://portal.azure.com). Następnie użyj przycisku analizy.

## <a name="cookies"></a>Włącz pliki cookie innych firm
  Zobacz [wyłączania pliki cookie innych firm](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), ale Zwróć uwagę, musimy **Włącz** je.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

