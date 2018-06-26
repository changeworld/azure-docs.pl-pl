---
title: Rozwiązywanie problemów z analizy w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: 'Problemy z analizy usługi Application Insights? Zacznij tutaj. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: eeda0fa6ad8faa05baf0a9344e958d298fb80d8e
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936687"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Rozwiązywanie problemów z analizą w usłudze Application Insights
Problemy z [Application Insights Analytics](app-insights-analytics.md)? Zacznij tutaj. Analytics to narzędzie wyszukiwania zaawansowanego w Azure Application Insights.

## <a name="limits"></a>Limity
* Obecnie wyniki zapytania są ograniczone nieco dłużej niż przez tydzień w ciągu ostatnich danych.
* Firma Microsoft testuje w przeglądarkach: najnowsze wersje programu Chrome, Microsoft Edge i przeglądarki Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Rozszerzenia znanych niezgodne przeglądarki
* Ghostery

Wyłączyć rozszerzenie, lub użyj innej przeglądarki.

## <a name="e-a"></a> "Nieoczekiwany błąd"
![Wystąpił nieoczekiwany błąd ekranu](./media/app-insights-analytics-troubleshooting/010.png)

Wystąpił błąd wewnętrzny podczas wykonywania portalu — nieobsługiwany wyjątek.

* Czyszczenie pamięci podręcznej przeglądarki. 

## <a name="e-b"></a>403 ... Spróbuj załadować ponownie
![403 ... Spróbuj załadować ponownie](./media/app-insights-analytics-troubleshooting/020.png)

Wystąpił błąd (podczas uwierzytelniania lub podczas generowania tokenu dostępu) związany z uwierzytelnianiem. Portalu może nie ma możliwości odzyskiwania bez zmiany ustawienia przeglądarki.

* Sprawdź [pliki cookie innych firm są włączone](#cookies) w przeglądarce. 

## <a name="authentication"></a>403 ... Sprawdź strefy zabezpieczeń
![403 ...podjęciem ponownej próby upewnij strefy zabezpieczeń](./media/app-insights-analytics-troubleshooting/030.png)

Wystąpił błąd (podczas uwierzytelniania lub podczas generowania tokenu dostępu) związany z uwierzytelnianiem. Portalu może nie ma możliwości odzyskiwania bez zmiany ustawienia przeglądarki.

1. Sprawdź [pliki cookie innych firm są włączone](#cookies) w przeglądarce. 
2. Czy do otwarcia portalu użyto ulubionych, zakładki lub zapisanego linku? Czy zalogowano się przy użyciu innych poświadczeń niż użyte podczas zapisywania linku?
3. Spróbuj użyć okna przeglądarki w trybie prywatnym/incognito (po zamknięciu wszystkich tych okien). Musisz podać swoje poświadczenia. 
4. Otwiera inne okno przeglądarki (zwykłej) i przejdź do [Azure](https://portal.azure.com). Wyloguj się. Następnie otwórz hiperlink i zaloguj się z prawidłowymi poświadczeniami.
5. Użytkownicy przeglądarek Microsoft Edge i Internet Explorer mogą również otrzymać ten błąd, gdy ustawienia zaufanej strefy nie są obsługiwane.
   
    Sprawdź zarówno [portal analityka](https://portal.azure.com) i [portalu usługi Azure Active Directory](https://portal.azure.com) znajdują się w tej samej strefie zabezpieczeń:
   
   * W programie Internet Explorer Otwórz **Opcje internetowe**, **zabezpieczeń**, **Zaufane witryny**, **witryny**:
     
     ![Okno dialogowe Opcje internetowe, dodawania witryny do zaufanych witryn](./media/app-insights-analytics-troubleshooting/033.png)
     
     Na liście witryn internetowych, jeśli uwzględnione są którekolwiek z następujących adresów URL, upewnij się, że uwzględniono również pozostałe:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Nie znaleziono zasobu
![404 ... nie można odnaleźć zasobu](./media/app-insights-analytics-troubleshooting/040.png)

Zasób aplikacji została usunięta z usługi Application Insights i nie jest już dostępny. Może to nastąpić, jeśli zapisane adres URL do strony analityka.

## <a name="e-e"></a>403 ... Brak autoryzacji
![403 ... nieautoryzowane](./media/app-insights-analytics-troubleshooting/050.png)

Nie masz uprawnień do otwierania tej aplikacji w module analiz.

* Czy został wyświetlony link od kogoś innego Poproś o upewnij się, że jesteś w [czytniki lub współautorzy dla tej grupy zasobów](app-insights-resources-roles-access-control.md).
* Został zapisany przy użyciu innych poświadczeń łącze? Otwórz [portalu Azure](https://portal.azure.com), wyloguj się, a następnie spróbuj to łącze ponownie, podając prawidłowe poświadczenia.

## <a name="html-storage"></a>403 ... Magazyn HTML5
Portalu używa HTML5 localStorage i sessionStorage.

* Przeglądarki Chrome: Ustawienia, ochrony prywatności, ustawienia zawartości.
* Internet Explorer: Opcje internetowe, karta Zaawansowane, zabezpieczeń, Włącz Magazyn DOM

![403 ... Spróbuj włączyć magazyn HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Nie znaleziono subskrypcji
![404 ... Nie znaleziono subskrypcji](./media/app-insights-analytics-troubleshooting/070.png)

Adres URL jest nieprawidłowy. 

* Otwórz zasobów aplikacji w [portalu Application Insights](https://portal.azure.com). Następnie użyj przycisku Analytics.

## <a name="e-h"></a>404 ... strona nie istnieje.
![404 ... Strona nie istnieje.](./media/app-insights-analytics-troubleshooting/080.png)

Adres URL jest nieprawidłowy.

* Otwórz zasobów aplikacji w [portalu Application Insights](https://portal.azure.com). Następnie użyj przycisku Analytics.

## <a name="cookies"></a>Włącz pliki cookie innych firm
  Zobacz [wyłączania pliki cookie innych firm](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), ale musimy powiadomienia **włączyć** je.


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

