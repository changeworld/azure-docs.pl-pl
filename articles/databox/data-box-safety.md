---
title: Bezpieczeństwo skrzynki danych platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano konwencje bezpieczeństwa, wskazówki i zagadnienia oraz wyjaśniono, jak bezpiecznie zainstalować i obsługiwać platformę Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: db6e2eadbf19d78a203a4d4ba6111ad88430b192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985942"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Bezpieczne instalowanie i obsługa skrzynki danych platformy Azure
![Ikona](./media/data-box-safety/warning_icon.png)
![ostrzeżenia Przeczytaj](./media/data-box-safety/read_safety_and_health_information_icon.png) ikonę powiadomienia o bezpieczeństwie **PRZECZYTAJ INFORMACJE O BEZPIECZEŃSTWIE I ZDROWIU**

Przeczytaj wszystkie informacje dotyczące bezpieczeństwa w tym artykule przed użyciem usługi Azure Data Box. Nieprzestrzeganie instrukcji może spowodować pożar, porażenie prądem elektrycznym lub inne obrażenia lub uszkodzenie właściwości.

## <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa
Oto ikony, które można znaleźć podczas przeglądania środków ostrożności, które należy przestrzegać podczas konfigurowania i uruchamiania pola danych.

| Ikona | Opis |
|:--- |:--- |
| ![Niebezpieczna](./media/data-box-safety/warning_icon.png) **ikona NIEBEZPIECZEŃSTWO!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, doprowadzi do śmierci lub poważnych obrażeń. To słowo sygnałowe ma być ograniczone do najbardziej ekstremalnych sytuacji. |
| ![Ikona](./media/data-box-safety/warning_icon.png) ostrzeżenia **OSTRZEŻENIE!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować śmierć lub poważne obrażenia ciała. |
| ![Ikona](./media/data-box-safety/warning_icon.png) ostrzeżenia **UWAGA!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować niewielkie lub umiarkowane obrażenia. |
| ![Uwaga](./media/data-box-safety/notice_icon.png) **Ikona UWAGA:** |Wskazuje informacje uważane za ważne, ale nie związane z zagrożeniami. |
| ![Ikona](./media/data-box-safety/electrical_shock_hazard_icon.png) **porażenia prądem elektrycznym** |Wysokie napięcie. |
| ![Gruba](./media/data-box-safety/heavy_weight_hazard_icon.png) ikona **wagi ciężkiej** | |
| ![Brak ikony](./media/data-box-safety/no_user_serviceable_parts_icon.png) części z możliwością serwisowania **użytkownika Brak części z możliwością serwisowania przez użytkownika** |Nie należy do nich korzystać, chyba że zostanie odpowiednio przeszkolony. |
| ![Przeczytaj ikonę](./media/data-box-safety/read_safety_and_health_information_icon.png) powiadomienia o bezpieczeństwie **Przeczytaj najpierw wszystkie instrukcje** | |
| ![Wskazówka](./media/data-box-safety/tip_hazard_icon.png) **Niebezpieczeństwo** | |

## <a name="handling-precautions"></a>Środki ostrożności podczas postępowania

![Ikona](./media/data-box-safety/warning_icon.png) ![ostrzeżenia](./media/data-box-safety/electrical_shock_hazard_icon.png)![Ikona porażenia](./media/data-box-safety/no_user_serviceable_parts_icon.png) prądem elektrycznym Brak ikony części z możliwością serwisowania **użytkownika UWAGA** 

* Sprawdź, czy urządzenie *po otrzymaniu* nie ma uszkodzeń. Jeśli obudowa urządzenia jest uszkodzona, [skontaktuj się z pomocą techniczną firmy Microsoft,](data-box-disk-contact-microsoft-support.md) aby uzyskać zamiennik. Nie próbuj obsługiwać urządzenia. 
* Urządzenie jest wyposażone w śruby odporne na manipulacje. Jeśli podejrzewasz, że urządzenie działa nieprawidłowo, [skontaktuj się z pomocą techniczną firmy Microsoft,](data-box-disk-contact-microsoft-support.md) aby uzyskać zamiennik. Nie próbuj serwisować urządzenia. 
* Urządzenie nie zawiera części z możliwością serwisowania przez użytkownika. W środku znajdują się niebezpieczne poziomy napięcia, prądu i energii. Nie otwierać. Zwróć urządzenie do firmy Microsoft w celu obsługi.

![Ostrzeżenie](./media/data-box-safety/warning_icon.png) ![Ikona](./media/data-box-safety/heavy_weight_hazard_icon.png) wagi ciężkiej **OSTRZEŻENIE!** 

* W pełni skonfigurowana obudowa może ważyć do 22,7 kg (50 funtów); nie próbuj podnosić go samodzielnie.
* Przed przeniesieniem obudowy należy zawsze upewnić się, że dwie osoby są dostępne do obsługi wagi. Należy pamiętać, że jedna osoba próbująca podnieść tę wagę może doznać obrażeń.


![Ikona](./media/data-box-safety/warning_icon.png) ![ostrzeżenia](./media/data-box-safety/tip_hazard_icon.png) Wskazówka Hazard Icon **OSTRZEŻENIE!**
* Umieść urządzenie na płaskiej, twardej i stabilnej powierzchni, aby uniknąć potencjalnego zagrożenia końcówką.
* Sprzęt montowany w stelażu nie może być używany jako półki lub miejsca do pracy. Nie należy umieszczać skrzynki danych na urządzeniu montowanym w stelażu. Dodanie dowolnego rodzaju obciążenia do wydłużonej jednostki montowanego w stelażu może spowodować potencjalne zagrożenie końcówką, które może prowadzić do obrażeń ciała, śmierci lub uszkodzenia produktu.

![Ikona](./media/data-box-safety/warning_icon.png) ostrzeżenia **OSTRZEŻENIE!**

* Ustawić urządzenie w miejscu roboczym, co pozwala na odpowiednią cyrkulację powietrza wokół urządzenia.
* Urządzenie należy zainstalować w pomieszczeniach o kontrolowanej temperaturze, wolnej od zanieczyszczeń przewodzących i dopuścić do odpowiedniej cyrkulacji powietrza wokół urządzenia.
* Urządzenie należy przechowywać z dala od źródeł cieczy i nadmiernie wilgotnych środowisk.


## <a name="electrical-precautions"></a>Środki ostrożności dotyczące elektrycznego

![Ikona](./media/data-box-safety/warning_icon.png) ![ostrzeżenia](./media/data-box-safety/electrical_shock_hazard_icon.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

* Zapewnić bezpieczne podłączenie uziemienia elektrycznego do przewodu zasilającego. Przewód przemienny jest posiada trójprzewodową wtyczkę uziemiającą (wtyczkę z łańcem). Wtyczka ta pasuje tylko do ziemionego gniazdka sieciowego. Nie należy pokonać celu pin uziemienia.
* Biorąc pod uwagę, że wtyczka na przewodzie zasilającym jest głównym urządzeniem odłączającym, upewnij się, że gniazda znajdują się w pobliżu urządzenia i są łatwo dostępne.
* Odłącz przewód zasilający (pociągając za wtyczkę, a nie za przewód) i odłącz wszystkie kable, jeśli istnieje którykolwiek z następujących warunków:

    - Przewód zasilający lub wtyczka staje się postrzępiona lub w inny sposób uszkodzona.
    - Rozlewasz coś do obudowy urządzenia.
    - Urządzenie jest narażone na działanie deszczu lub nadmiaru wilgoci.
    - Urządzenie zostało upuszczone, a obudowa urządzenia jest uszkodzona.
    - Podejrzewasz, że urządzenie wymaga serwisu lub naprawy.
* Na stałe odłącz urządzenie przed jego przeniesieniem lub jeśli uważasz, że zostało uszkodzone w jakikolwiek sposób.
* Zapewnienie odpowiedniego źródła zasilania z zabezpieczeniem przed przeciążeniem elektrycznym, aby spełnić następujące specyfikacje zasilania:

    - Napięcie: 100 V AC do 240 V AC
    - Prąd: 6 A, maksymalnie
    - Częstotliwość: od 50 Hz do 60 Hz

![Ikona](./media/data-box-safety/warning_icon.png) ostrzeżenia **UWAGA:**

* To urządzenie zawiera baterie pastylkowe. Nie próbuj serwisować urządzenia. Baterie w tym urządzeniu nie nadają się do serwisowania przez użytkownika. 
* **Tylko dla personelu serwisowego**: Ryzyko wybuchu, jeśli bateria jest wymieniana przez nieprawidłowy typ. Zużyte baterie należy utylizować zgodnie z instrukcjami.

![Uwaga](./media/data-box-safety/notice_icon.png) **Ikona UWAGA:**

W celu prawidłowego działania urządzenia i zapobiegania uszkodzeniom produktu:

* Upewnij się, że przednie i tylne drzwi są całkowicie otwarte, gdy urządzenie jest uruchomione.

## <a name="regulatory-information"></a>Informacje prawne

Ta sekcja zawiera informacje prawne dotyczące usługi Azure Data Box, numer modelu regulacyjnego DB010.

To urządzenie jest:

- Oceniony jako sprzęt informatyczny (ITE), zaprojektowany do pracy w typowym środowisku pomieszczenia danych. Przydatność tego produktu do innych środowisk może wymagać dalszej oceny.
- Przeznaczone do użytku z urządzeniami informatycznymi zgodnymi z numerem LCL (UL, CSA, ETL itp.) oraz urządzeniami informatycznymi zgodnymi ze standardem IEC/EN 60950-1 lub IEC/EN 62368-1.
- Zaprojektowany do pracy w następującym środowisku. 
    - Temperatura pracy: od 10° do 35°C
    - Temperatura przechowywania: od -4° do 122° F (-20° do 50° C)
    - Wilgotność względna: od 15% do 85% (bez kondensowania) 
    - Wysokość robocza: Testowana do 6500 stóp (0 metrów do 2000 metrów)

Klasyfikacja zasilania elektrycznego znajduje się na etykiecie znamionowej urządzenia dostarczonej wraz z urządzeniem. 

![Uwaga](./media/data-box-safety/notice_icon.png) **Ikona UWAGA:** 

Zmiany lub modyfikacje urządzenia, które nie zostały wyraźnie zatwierdzone przez firmę Microsoft, mogą unieważnić uprawnienia użytkownika do obsługi urządzenia.

**KANADA i USA:**

![Uwaga](./media/data-box-safety/notice_icon.png) **Ikona UWAGA:** 

Urządzenie to zostało przetestowane i uznane za zgodne z ograniczeniami dla urządzeń cyfrowych klasy A, zgodnie z częścią 15 przepisów FCC. Limity te mają na celu zapewnienie odpowiedniej ochrony przed szkodliwymi zakłóceniami, gdy urządzenie jest eksploatowane w środowisku komercyjnym. Urządzenie to wytwarza, wykorzystuje i może emitować energię o częstotliwości radiowej, a jeśli nie jest zainstalowane i używane zgodnie z instrukcją obsługi, może powodować szkodliwe zakłócenia łączności radiowej. Działanie tego urządzenia w dzielnicy mieszkalnej może spowodować szkodliwe zakłócenia, w którym to przypadku użytkownik będzie zobowiązany do skorygowania zakłóceń na własny koszt.

To urządzenie jest zgodne z częścią 15 normy RSS zwolnione z licencji FCC i Industry Canada. Działanie podlega następującym dwóm warunkom: (1) urządzenie nie może powodować szkodliwych zakłóceń oraz (2) urządzenie musi akceptować wszelkie otrzymane zakłócenia, w tym zakłócenia, które mogą powodować niepożądane działanie urządzenia.

![Kanada](./media/data-box-safety/canada.png)

CZY ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, Stany Zjednoczone.

Stany Zjednoczone: (800) 426-9400

Kanada: (800) 933-4750

**UNIA EUROPEJSKA:**

[Zażądać kopii deklaracji zgodności UE](mailto:CSI_Compliance@microsoft.com).

![Ostrzeżenie:](./media/data-box-safety/warning_icon.png) **WARNING:** 

Jest to produkt klasy A. W środowisku domowym produkt ten może powodować zakłócenia radiowe, w którym to przypadku użytkownik może być zobowiązany do podjęcia odpowiednich środków.

**Unieszkodliwianie zużytych baterii oraz sprzętu elektrycznego i elektronicznego:**

![Ikona utylizacji baterii](./media/data-box-safety/battery_disposal_icon.png)

Ten symbol na produkcie lub jego bateriach lub opakowaniu oznacza, że tego produktu i wszelkich baterii, które zawiera, nie wolno wyrzucać wraz z odpadami domowymi. Zamiast tego należy przekazać tę kwestię do odpowiedniego punktu zbiórki w celu recyklingu baterii oraz sprzętu elektrycznego i elektronicznego. Ta selektywna zbiórka i recykling przyczynią się do ochrony zasobów naturalnych i zapobiegania potencjalnym negatywnym skutkom dla zdrowia ludzkiego i środowiska ze względu na możliwą obecność niebezpiecznych substancji w bateriach oraz elektrycznych i elektronicznych sprzętu, co może być spowodowane niewłaściwą utylizacją. Aby uzyskać więcej informacji o tym, gdzie zasnąć baterie oraz odpady elektryczne i elektroniczne, skontaktuj się z lokalnym urzędem miasta/gminy, serwisem utylizacji odpadów z gospodarstw domowych lub sklepem, w którym zakupiono ten produkt. Aby uzyskać dodatkowe informacje na temat WEEE, skontaktuj się z *\@microsoft.com.*

Ten produkt zawiera baterie pleedowe(-e).

Microsoft Irlandia Sandyford Ind Est Dublin D18 KX32 IRL

Numer telefonu: +353 1 295 3826

Numer faksu: +353 1 706 4110 

**Japonia**

![Japonia](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Po zapoznaniu się z tymi uwagami o bezpieczeństwie można skonfigurować i przekąsić urządzenie.

## <a name="next-steps"></a>Następne kroki

* [Kabel i podłącz skrzynkę danych](data-box-deploy-set-up.md)


