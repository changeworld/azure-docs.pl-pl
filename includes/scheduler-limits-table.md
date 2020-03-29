---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392394"
---
W poniższej tabeli opisano każdy z głównych przydziałów, limitów, wartości domyślnych i ograniczeń w usłudze Azure Scheduler.

| Zasób | Opis limitu |
| -------- | ----------------- |
| **Rozmiar zadania** | Maksymalny rozmiar zadania to 16 000. Jeśli put lub patch operacji powoduje rozmiar zadania większy niż ten limit, zwracany jest kod stanu 400 Bad Request. | 
| **Kolekcje zadań** | Maksymalna liczba kolekcji zadań na subskrypcję platformy Azure wynosi 200 000. | 
| **Zadania na kolekcję** | Domyślnie maksymalna liczba zadań to pięć zadań w kolekcji wolnych zadań i 50 zadań w kolekcji zadań standardowych. Można zmienić maksymalną liczbę zadań w kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone do wartości ustawionej w kolekcji zadań. Jeśli spróbujesz utworzyć więcej zadań niż maksymalny przydział zadań, żądanie zakończy się niepowodzeniem z kodem stanu konfliktu 409. | 
| **Czas rozpoczęcia** | Maksymalny "czas rozpoczęcia" wynosi 18 miesięcy. |
| **Zakres nawrotu** | Maksymalny okres nawrotu wynosi 18 miesięcy. | 
| **Częstotliwości** | Domyślnie maksymalny przydział częstotliwości wynosi jedną godzinę w kolekcji wolnych zadań i jedną minutę w kolekcji zadań standardowych. <p>Maksymalną częstotliwość w kolekcji zadań można zmniejszyć niż maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone do wartości ustawionej w kolekcji zadań. Jeśli spróbujesz utworzyć zadanie o wyższej częstotliwości niż maksymalna częstotliwość w kolekcji zadań, żądanie zakończy się niepowodzeniem z kodem stanu konfliktu 409. | 
| **Rozmiar ciała** | Maksymalny rozmiar treści dla żądania wynosi 8192 znaków. |
| **Zażądaj rozmiaru adresu URL** | Maksymalny rozmiar adresu URL żądania to 2048 znaków. |
| **Liczba nagłówków** | Maksymalna liczba nagłówków wynosi 50 nagłówków. | 
| **Zagregowany rozmiar nagłówka** | Maksymalny łączny rozmiar nagłówka wynosi 4096 znaków. |
| **Limit czasu** | Limit czasu żądania jest statyczny, czyli nie można go skonfigurować. i wynosi 60 sekund dla akcji HTTP. W przypadku dłuższych operacji należy postępować zgodnie z protokołami asynchronizacyjnym HTTP. Na przykład natychmiast zwróć 202, ale kontynuuj pracę w tle. | 
| **Historia zadania** | Maksymalna treść odpowiedzi przechowywana w historii zadań wynosi 2048 bajtów. |
| **Zatrzymanie historii zadań** | Historia zadań jest przechowywana przez okres do dwóch miesięcy lub do ostatnich 1000 egzekucji. | 
| **Zapełnione i uszkodzone zatrzymanie zadań** | Wykonane i uszkodzone zadania są przechowywane przez 60 dni. |
||| 

