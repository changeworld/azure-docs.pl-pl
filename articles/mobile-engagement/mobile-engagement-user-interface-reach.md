---
title: Interfejs użytkownika usługi Azure Mobile Engagement - Reach
description: Dowiedz się, jak dotrzeć do użytkowników aplikacji z powiadomień wypychanych przy użyciu usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Jak dotrzeć do użytkowników aplikacji przy użyciu powiadomień wypychanych
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

W tym artykule opisano **OSIĄGNĄĆ** karcie **Mobile Engagement** portalu. Możesz użyć **Mobile Engagement** portalu do monitorowania i zarządzania aplikacjami mobilnymi. Należy pamiętać, że można uruchomić przy użyciu portalu należy najpierw utworzyć **usługi Azure Mobile Engagement** konta. Aby uzyskać więcej informacji, zobacz [utworzyć konto usługi Azure Mobile Engagement](mobile-engagement-create.md).

Sekcja Reach interfejsu użytkownika jest wypychane kampanii narzędzie do zarządzania można utworzyć/edytowanie/aktywować/Zakończ/monitor i uzyskać statystyki kampanii obejmujących wysyłanie powiadomień wypychanych i funkcji, które są dostępne za pośrednictwem interfejsu API Reach (i niektóre elementy niski poziom Push interfejsu API) . Należy pamiętać, czy używane są interfejsy API lub interfejsu użytkownika, konieczne będzie zintegrować zarówno usługi Azure Mobile Engagement i Reach do aplikacji dla poszczególnych platform przy użyciu zestawu SDK można osiągnąć kampanii.

> [!NOTE]
> Wiele sekcji **Mobile Engagement** zawierają interfejsu użytkownika portalu **Pokaż Pomoc** przycisku. Naciśnij ten przycisk, aby uzyskać dodatkowe informacje kontekstowe o sekcji.
> 
> 

## <a name="four-types-of-push-notifications"></a>Cztery typy powiadomień wypychanych
1. Anonse — pozwalają wysyłać reklamy przez sieć do użytkowników, którzy przekierować je do innej lokalizacji w aplikacji lub w celu wysłania ich sklep poza aplikacji lub strony sieci Web. 
2. Ankiety — umożliwiają zbieranie informacji od użytkowników końcowych za pośrednictwem pytań.
3. Wypychania danych — umożliwia wysyłanie pliku danych binarnych lub base64. Informacje zawarte w wypychanych danych są wysyłane do aplikacji, aby zmodyfikować bieżącego środowiska użytkowników w aplikacji. Aplikacja musi być w stanie przetwarzać dane w wypychanych danych.

## <a name="campaign-details"></a>Szczegóły kampanii
Można edytować, klonowanie, usunąć lub aktywowania kampanii, które nie zostały jeszcze aktywowane, ustawiając kursor nad ich nazwy lub można kliknąć, aby je otworzyć. Można sklonować kampanii, które zostały już aktywowana, ustawiając kursor nad ich nazwy lub można kliknąć, aby je otworzyć. Jednak nie można zmienić kampanii, gdy został już aktywowany.

![Reach1][18]

## <a name="reach-feedback"></a>Osiągnąć opinii
Polecenie **statystyki** Aby wyświetlić szczegóły kampanii Reach. **Proste** widok zawiera wizualną reprezentację w postaci wykresu słupkowego kolumny o co się stało po kampanii został aktywowany. **Zaawansowane** widoku szczegółowe bardziej szczegółowe informacje na temat kampanii wypychania. Te informacje nie będą dostępne w przypadku wysyłania kampanii testowej tj wypychanej wysłane do urządzenia testu. Oto, jak należy interpretować te szczegóły:

1. **Wypychana** — określa to liczba komunikatów wypchniętych do urządzeń. Ta liczba będzie zależeć od docelowych odbiorców określone podczas tworzenia kampanii wypychania. Jeśli nie określono żadnych docelowych odbiorców, następnie tego wypychane będą wysyłane do zarejestrowanych urządzeń. Podobnie jak inne usługi wypychania, firma Microsoft nie powiadomienia wypychane bezpośrednio do urządzenia, ale zamiast tego Wypchnij je do odpowiednich platform określonych usług powiadomień wypychanych (PNS - WNS-APNS/GCM) tak, aby ich dostarczenia powiadomienia do urządzeń. 
2. **Dostarczone** — określa liczbę wiadomości, które pomyślnie są dostarczane przez system powiadomień platformy na urządzeniu i potwierdzone jako odebrane przez zestaw SDK usługi Mobile Engagement. 
   
   *Przyczyny dostarczone liczba była mniejsza niż liczba wciśnięcia:*
   
   1. Jeśli użytkownik odinstalował aplikacji z urządzenia, ale systemu powiadomień platformy nie może ustalić o nim w chwili wyślemy powiadomienia wypychanego do systemu powiadomień platformy, komunikat zostanie usunięty.
   2. Jeśli urządzenie jest aplikacja, ale z samymi urządzeniami były w trybie offline przez dłuższy czas, PNS zakończy się niepowodzeniem na dostarczenie wiadomości do urządzenia. 
   3. Jeśli dostarczenie wiadomości do urządzenia, ale zestaw Mobile Engagement SDK w aplikacji nie rozpoznaje zawartości komunikatu, jego porzuca tej wiadomości. Może to się zdarzyć, jeśli dostosowywania powiadomień w aplikacji generuje wyjątek, który mamy catch w zestawu SDK i upuszczanie wiadomości. Można to także wystąpić w przypadku aplikacji na urządzeniu jest używana jest wersja zestaw Mobile Engagement SDK, który nie jest w stanie zrozumieć nowszej wersji wiadomości wypychanych wysyłane z platformy, ale jest to tylko wtedy, gdy aplikacja został uaktualniony po powiadomienia została wysłana z t Platforma service on. **Zaawansowane** kartę informuje, ile komunikatów zostały usunięte. 
   4. Na urządzeniach z systemem iOS wiadomości czasami nie dostarczenie czy albo urządzenie znajduje się na niskim poziomie naładowania baterii, czy aplikacja zużywa dużo mocy podczas przetwarzania zdalnego powiadomienia. Jest to ograniczenie urządzeń z systemem iOS.   
3. **Wyświetlane** — określa liczbę wiadomości, które pomyślnie zostały przedstawione dla użytkownika aplikacji na urządzeniu w formularzu systemu powiadomienia wypychane/out z — aplikacji w Centrum powiadomień lub powiadomienie w aplikacji w aplikacji mobilnej.  **Zaawansowane** kartę informuje o ile zostały powiadomień systemowych i ile były powiadomienia w aplikacji. 
   
   *Liczba powody mogą być wyświetlane jest mniejsza niż liczba dostarczone (oczekiwanie, który będzie wyświetlany)*
   
   1. Gdyby kampanię powiadomień datę końcową w nim następnie jest możliwe, że powiadomienie zostało dostarczone, ale po chwili pochodzi otworzyć i wyświetlić je użytkownikowi aplikacji, on już wygasł, nigdy nie została wyświetlona.   
   2. Jeśli powiadomienie jest powiadomienie w aplikacji, a następnie powiadomienie jest wyświetlane tylko wtedy, gdy aplikacji użytkownik otwiera aplikację. W przypadkach, gdy użytkownik aplikacji nie otworzyła aplikacji zestaw SDK będzie zgłaszać czy powiadomienie zostało dostarczone, ale nie została jeszcze wyświetlany, dopóki otwarciu aplikacji. 
   3. Jeśli powiadomienie jest powiadomienie w aplikacji i skonfigurowane do pokazania na określone działanie/ekranu, a następnie również powiadomienia będą raportowane jako wydana, ale nie zostały jeszcze dostarczone do użytkownik otwiera aplikację na określonych ekranu. 
4. **Interakcje użytkownika** — określa liczbę wiadomości, które ma interakcją użytkowników aplikacji z i będzie zawierać komunikaty, które są akcje lub Zakończono. 
   
   * *Użytkownik aplikacji może wykonać akcję powiadomień w jednej z następujących sposobów:*
     
     1. Jeśli powiadomienie jest powiadomienie systemu/out elementu aplikacji lub powiadomienie w aplikacji, wysyłane jako tylko do powiadomień, a następnie użytkownik aplikacji kliknie powiadomienie.
     2. Jeśli powiadomienie jest powiadomienie w aplikacji z tekstu lub widok sieci web lub sondy aplikacji użytkownik kliknie przycisk akcji w powiadomienia.
     3. Jeśli powiadomienie jest powiadomienie w aplikacji z widoku sieci web, a następnie kliknięciu aplikacji przy użyciu adresu URL sieci web tylko w widoku [Android]
   * *Użytkownik aplikacji może wyjść powiadomień w jednej z następujących sposobów:*
     
     1. Kliknięcie przycisku Zamknij powiadomienie bezpośrednio. 
     2. Szybko przesuwając optymalizacji lub usunięcie powiadomienia. 
     3. Powiadomienia w aplikacji przy użyciu zawartości tekstu/sieci web i sond zwykle są wyświetlane użytkownikowi aplikacji w dwóch etapach. Najpierw widzą powiadomienie, a po kliknięciu na nim, zobacz temat kolejnych zawartości tekstu/sieci web/sondowania. Użytkownik aplikacji może wyjść powiadomień w jednej z następujących czynności i szczegółów w widoku Zaawansowane przechwytuje to. 
5. **Których wykonano akcje** — to określa liczbę wiadomości, które zostały jawnie których wykonano akcje przez użytkownika aplikacji. Jest to najbardziej interesujące liczba jako informuje ilu użytkowników aplikacji zostały zainteresowanych przez komunikat, który zostanie umieszczony w powiadomieniu. 

> [!NOTE]
> W systemie iOS & Windows otwórz platformy, jeśli użytkownik ma aplikacji i kampanii został kampanii "W każdej chwili", a następnie jest możliwe, zarówno z aplikacji i powiadomienia w aplikacji są wyświetlane w tym samym czasie. Może to spowodować liczbą wyświetlane wyższe niż dostarczone. Jeśli użytkownik wchodzi w interakcję lub akcje powiadomienia, a następnie nawet liczba interakcji użytkownika/Actioned może być większa niż dostarczone. 
> 
> 

![Reach2][19]

## <a name="see-also"></a>Zobacz także
* [Pojęcia][Link 6]
* [Usługa Przewodnik rozwiązywania problemów][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

