---
title: Słownik terminów dla niestandardowych Vision usług — usług Azure kognitywnych | Dokumentacja firmy Microsoft
description: Słownik terminów wizji niestandardowe usługi.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 871617ce3c1c5a84df746c0c7d87c113b3a6f354
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347876"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Słownik terminów usługi wizji niestandardowe

Poniżej przedstawiono niektóre terminów używanych w usługi wizji niestandardowych i ich znaczenie.

## <a name="classifier"></a>Klasyfikator

Klasyfikator jest tworzonych przy użyciu usługi wizji niestandardowe, za pomocą kilka obrazów uczenia modelu. Po zakończeniu szkolenia nowy klasyfikator pojawi się punkt końcowy oceny (HTTPS) można dodać do aplikacji. Każdy klasyfikatora tworzonych w własnego projektu, a po zalogowaniu się można wyświetlić wszystkie projekty.

## <a name="domain"></a>Domena

Podczas tworzenia projektu, należy wybrać "domeny" dla tego projektu. Domena optymalizuje klasyfikatora dla określonego typu obiektu w obrazów. Na przykład w przypadku danego scenariusza klasyfikowania między obrazy koła firmy apple i obrazy ciasto marchewkowe, wybierz domenę "Żywności". Jeśli nie wiesz, która domena, aby wybrać, następnie wybierz domenę "Ogólne".

- **Domena żywności.** Zoptymalizowana pod kątem parownice dostępne w menu w restauracji. Nie zostało zoptymalizowane rozpoznawania poszczególnych owoców lub warzyw. Do klasyfikowania fotografie poszczególnych owoców lub warzyw, w tym celu użyć ogólnego domeny.
- **Domena punkty.** Zoptymalizowana pod kątem rozpoznawalnych punkty orientacyjne, fizyczne i sztucznych. Ta domena działa najlepiej, gdy punkty jest widoczny w fotografii, nawet jeśli punkty nieco jest zablokowane przez grupy osób stwarza przed nim.
- **Domena sprzedaży detalicznej.** Zoptymalizowana pod kątem klasyfikującego obrazów w katalogu zakupów lub zakupów witryna sieci Web. Wysokiej precyzji przy klasyfikacji sukienki, spodnie, koszule itp., następnie użyć domeny sprzedaży detalicznej.
- **Domena dla dorosłych.** Zoptymalizowane, aby dokładniej zdefiniować między zawartość dla dorosłych i zawartości z systemem innym niż osoba dorosła. Na przykład jeśli chcesz zablokować obrazów osób w kąpieliskach kolory tej domeny umożliwia tworzenie niestandardowych klasyfikatora w tym.
- **Ogólne domeny.** Dobrze nadaje się do szerokiej gamy zadań klasyfikacji obrazu.

Modele generowane przez **compact domen** można eksportować z funkcji eksportu iteracji. Są one zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatory skompilowanej za pomocą compact domeny mogą być nieco mniej dokładne standardowej w domenę z tym samym ilością danych szkoleniowych. Zależności te są duże, aby uruchomić lokalnie w niemal w czasie rzeczywistym. 

## <a name="training-image"></a>Obraz szkolenia

Utworzenie klasyfikatora programu wysokiej precyzji, usługa wizji niestandardowy musi kilka obrazów szkolenia. Obraz szkolenia jest fotografii obraz niestandardowy usługa wizji klasyfikowania. Na przykład do klasyfikowania pomarańczy, będzie potrzebny do przekazywania kilka obrazów pomarańczy usługą wizji niestandardowe aby umożliwić usłudze utworzenie klasyfikatora, który może rozpoznawać pomarańczy. Zaleca się co najmniej 30 obrazów na tag.

## <a name="iteration"></a>Iteracji

Każdy czasu, gdy pociąg lub ponownie uczenia z klasyfikatora, Utwórz nową iterację modelu. Zachowaj mamy kilka ostatnich iteracji, aby można porównywać postęp w czasie. Możesz usunąć wszelkie iteracji, które już być przydatne. Należy pamiętać, usuwanie iteracji jest trwały, czy należy również usunąć wszystkie obrazy lub zmiany, które były unikatowe dla tej iteracji. 

## <a name="workspace"></a>Obszar roboczy

Obszar roboczy zawiera wszystkie obrazy szkolenia i odzwierciedla wszystkie zmiany z z najnowszych iteracji, takich jak usunięte lub dodane obrazów. Szkolenie z klasyfikatora, możesz utworzyć nową iterację Twojej klasyfikatora przy użyciu obrazów w obszarze roboczym.

## <a name="tags"></a>Tagi

Używaj tagów do etykiety obiektów obrazów szkolenia. Jeśli tworzysz klasyfikatora do identyfikacji psów i kucyków spowodowałaby tag "dog" na obrazów, które zawierają skalary, znacznikiem "kuce", na obrazów, które zawierają kuców, a jednocześnie "kot" i "kuce" obrazów zawierających dog i konika na.

## <a name="evaluation"></a>Ocena

Po ma uczony z klasyfikatora, można przesyłać żadnego obrazu do oceny, przy użyciu punkt końcowy https wygenerowany automatycznie. Twoje klasyfikatora zwraca zestaw przewidywane tagów w kolejności zaufania.

## <a name="predictions"></a>Prognozy

Zgodnie z klasyfikatora akceptuje nowych obrazów do klasyfikowania, są przechowywane obrazy dla Ciebie. Te obrazy umożliwia zwiększyć dokładność Twojej klasyfikatora przez poprawnie znakowanie źle przewidywane obrazów. Następnie można tych nowych obrazów ponownie uczenia z klasyfikatora.

## <a name="precision"></a>Dokładność

Podczas klasyfikowania obrazu, jak prawdopodobnie jest Twoje klasyfikatora poprawnie klasyfikowania obrazu? Poza wszystkie obrazy używane w celu przeszkolenia klasyfikatora (psów i kucyków) jaki procent czy modelu wyświetlony poprawna? 99 tagi poprawne poza 100 obrazów zapewnia dokładność 99%.

## <a name="recall"></a>Odwołaj

Poza wszystkich obrazów, które powinny mieć zostały poprawnie klasyfikowane ile może Twoje klasyfikatora ustalić poprawnie? Odwołania 100% oznacza to, czy wystąpiły 38 obrazy dog obrazy używane w celu przeszkolenia klasyfikatora, 38 psów zostały znalezione przez klasyfikatora.

## <a name="settings"></a>Ustawienia

Istnieją dwa typy ustawień, ustawienia na poziomie projektu i na poziomie użytkownika.

- Ustawienia na poziomie projektu: 
  
  Zastosuj ustawienia na poziomie projektu do projektu lub klasyfikatora. Obejmują one:

   - Projektu domeny
   - Nazwa projektu
   - Opis elementu projektu
   - Użycie:
      - Liczba obrazów szkolenia
      - Znaczniki utworzone
      - Liczba iteracji utworzone

- Ustawienia na poziomie użytkownika: 
   - Klucze subskrypcji: jeden dla szkolenia, jeden dla oceny/prognozowania.
   - Użycie:
      - Liczba projektów utworzonych
      - Liczba wywołań interfejsu API w wersji ewaluacyjnej/prognozowania.
