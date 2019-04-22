---
title: Słownik terminów — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Słownik terminów usługi Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 3a34af77a2806ceb56e939e2b153f2e68bba61cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048940"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Słownik terminów dla usługi Custom Vision Service

Poniżej przedstawiono niektóre terminy często używane w usłudze Custom Vision Service:

## <a name="classifier"></a>Klasyfikator

Klasyfikator to model tworzonych za pomocą usługi Custom Vision Service przy użyciu kilku uczone obrazy. Po zakończeniu szkolenia nowych klasyfikatora otrzymasz endpoint oceny (HTTPS), można dodać do swojej aplikacji. Każdy klasyfikatora, które tworzysz jest we własnym projekcie i można wyświetlić wszystkie projekty po zalogowaniu.

## <a name="domain"></a>Domain

Podczas tworzenia projektu, możesz wybrać "domena" dla tego projektu. Domeny optymalizuje klasyfikatora dla określonego typu obiektu do obrazów. Na przykład w przypadku danego scenariusza klasyfikowania między obrazów kołowy firmy apple, a obrazy ciasto marchewkowe, wybierz domenę "Jedzenie". Jeśli masz pewności, która domena, do wyboru, a następnie wybierz pozycję "Generic" domeny.

- **Domena żywności.** Zoptymalizowane pod kątem płytki, który będzie wyświetlony menu restauracji. Nie zoptymalizowanym rozpoznawania poszczególnych owoców lub warzyw. Do klasyfikowania fotografie poszczególne owoce lub warzyw, należy użyć domeny ogólnego do tego celu.
- **Domena charakterystycznych elementów krajobrazu.** Zoptymalizowane pod kątem rozpoznawalnych charakterystycznych elementów krajobrazu, zarówno naturalnych, jak i sztucznych. Ta domena działa najlepiej, gdy charakterystycznych elementów krajobrazu jest widoczny w fotografii, nawet wtedy, gdy charakterystycznych elementów krajobrazu to nieco zablokowane przez grupy osób stanowiące przed nim.
- **Domena sprzedaży detalicznej.** Zoptymalizowane pod kątem klasyfikowania obrazów w katalogu zakupów lub zakupów witryna sieci Web. Bardzo precyzyjnej podczas klasyfikowania sukienki, spodnie, koszule itp., następnie użyć domeny sprzedaży detalicznej.
- **Ogólne domeny.** Dobrze przystosowane do potrzeb szerokiej gamy zadań klasyfikacji obrazów.

Modeli generowanych przez **compact domen** można wyeksportować za pomocą funkcji eksportu iteracji. Są one zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Klasyfikatorów utworzonych za pomocą compact domeny mogą być nieco mniej dokładne standardowej w domenę przy użyciu tej samej ilości danych szkoleniowych. Kosztem jest, że są one wystarczająco mała, aby uruchomić lokalnie w niemal w czasie rzeczywistym. 

## <a name="evaluation"></a>Ocena

Gdy ma skonfigurowanych pod kątem klasyfikatora, możesz przesłać dowolnego obrazu do oceny przy użyciu punktu końcowego https wygenerowany automatycznie. Klasyfikatora zwraca zestaw przewidywane tagów w kolejności zaufania.

## <a name="iteration"></a>Iteracja

Co godzinę Train lub ponownie uczyć klasyfikatory, możesz utworzyć nową iterację modelu. Przechowujemy kilka ostatnich iteracji, aby umożliwić porównywanie postęp wraz z upływem czasu. Można usuwać dowolną iterację, które nie są już być przydatne. Należy pamiętać, że usunięcie iteracji jest trwały i zostaną również usunięte wszelkie obrazy i zmiany, które są unikatowe dla tej iteracji. 

## <a name="precision"></a>Precyzja

Podczas klasyfikowania obrazu, jak prawdopodobnie jest klasyfikatory poprawnie klasyfikowania obrazów? Poza wszystkie obrazy używane do trenowania klasyfikatora (psów i kuców) jaki procent czy modelu wyświetlony poprawne? 99 prawidłowe tagi poza 100 obrazów zapewnia dokładność 99%.

## <a name="predictions"></a>Prognozy

Jak klasyfikatora akceptuje nowych obrazów do klasyfikowania, obrazy są przechowywane dla Ciebie. Te obrazy można użyć, aby poprawić dokładność klasyfikatora poprawnie, oznaczając źle przewidziane obrazów. Następnie można te nowe obrazy ponownie uczyć klasyfikatory.

## <a name="recall"></a>Odwołaj

Poza wszystkie obrazy, które powinny mieć został poprawnie klasyfikowane ile klasyfikatora zidentyfikować prawidłowo? Odwołania 100% oznacza to, jeśli wystąpiły 38 pies obrazy zawierają obrazy używane do trenowania klasyfikatora, znaleziono 38 psy przez klasyfikatora.

## <a name="settings"></a>Ustawienia

Istnieją dwa rodzaje ustawień i ustawienia na poziomie projektu i ustawień na poziomie użytkownika.

- Ustawienia na poziomie projektu:
  
  Ustawienia na poziomie projektu mają zastosowanie do projektu lub klasyfikatora. Obejmują one:

   - Projekt do domeny
   - Nazwa projektu
   - Opis projektu
   - Użycie:
      - Liczba obrazów do szkolenia
      - Liczba tagów utworzone
      - Liczba iteracji utworzone

- Ustawienia na poziomie użytkownika: 
   - Klucze subskrypcji: jeden dla szkolenia, jeden dla oceny/prognozy.
   - Użycie:
      - Liczba projektów utworzonych
      - Liczba wywołań interfejsu API w wersji ewaluacyjnej/prognozy.

## <a name="tags"></a>Tagi

Za pomocą tagów można oznaczyć obiekty do obrazów do szkolenia. Jeśli tworzysz klasyfikatora do identyfikowania psów i kucyków możesz umieścić tag "dog" na obrazach, które zawierają psy, znacznikiem "pony", na obrazach, które zawierają kuców i zarówno "dog" i "pony" obrazów zawierających pies i pony.

## <a name="training-image"></a>Obraz szkolenia

Aby utworzyć klasyfikatora dużej dokładności, usługi Custom Vision Service wymaga kilku uczone obrazy. Obraz szkolenia jest fotografii obraz, który chcesz, aby usługi Custom Vision Service do klasyfikowania. Na przykład można klasyfikować Pomarańcze, należałoby Przekaż kilka obrazów Pomarańcze do usługi Custom Vision Service, aby umożliwić usłudze klasyfikatora, który może rozpoznawać Pomarańcze utworzyć. Zalecamy co najmniej 30 obrazy na tag.

## <a name="workspace"></a>Obszar roboczy

Obszar roboczy zawiera wszystkie obrazy szkolenia i odzwierciedla wszystkie zmiany z Twojej ostatnich iteracji, takich jak usunięte lub dodane obrazów. Możesz uczyć klasyfikatory, możesz utworzyć nową iterację klasyfikatora, przy użyciu obrazów obecny w obszarze roboczym.