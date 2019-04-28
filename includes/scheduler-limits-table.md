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
ms.openlocfilehash: b3788ede23a423bebf96661ea88b227bfb5fdf4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531281"
---
W poniższej tabeli opisano każdy z głównych przydziały, limity, wartości domyślne i ograniczenia w usłudze Azure Scheduler.

| Resource | Opis elementu limit |
| -------- | ----------------- |
| **Rozmiar zadania** | Maksymalny rozmiar zadania to 16 000. Jeśli operacja PATCH lub PUT powoduje przekracza ten limit rozmiaru zadania, 400 Niewłaściwe żądanie zwróciła kod stanu. | 
| **Kolekcje zadań** | Maksymalna liczba kolekcji zadań na subskrypcję platformy Azure wynosi 200 000. | 
| **Liczba zadań na kolekcję** | Domyślnie maksymalna liczba zadań jest pięć zadań w kolekcji zadań w warstwie bezpłatna i 50 zadań w kolekcji standardowych zadań. <p>Możesz zmienić maksymalną liczbę zadań na kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone do wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć większą liczbę zadań niż zadania maksymalny limit przydziału, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. | 
| **Czas na czas rozpoczęcia** | Wartość maksymalna "przyszedł czas na czas" to 18 miesięcy. |
| **Zakres cyklu** | Zakres maksymalna wartość cyklu jest 18 miesięcy. | 
| **Częstotliwość** | Domyślnie przydział maksymalnej częstotliwości jest jedną godzinę w kolekcji zadań w warstwie bezpłatna i jedną minutę w kolekcji standardowych zadań. <p>Można wprowadzić maksymalną częstotliwość na kolekcji zadań niższa niż wartość maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone do wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć zadanie z częstotliwością wyższa niż maksymalna częstotliwość na kolekcji zadań, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. | 
| **Rozmiar treści** | Rozmiar maksymalny treść żądania jest 8192 znaków. |
| **Rozmiar adresu URL żądania** | Maksymalny rozmiar, w adresie URL żądania jest 2048 znaków. |
| **Liczba w nagłówku** | Liczba maksymalna nagłówka to 50 nagłówków. | 
| **Rozmiar nagłówka agregacji** | Rozmiar maksymalny łączny nagłówka wynosi 4096 znaków. |
| **limit czasu** | Limit czasu żądania jest statyczna, oznacza to, że nie można konfigurować. i wynosi 60 sekund dla akcji HTTP. W przypadku dłużej uruchomionej operacji wykonaj asynchroniczne protokołów HTTP. Na przykład niezwłocznie zawrócone 202, ale nadal działa w tle. | 
| **Historia zadania** | Treść odpowiedzi maksymalna przechowywanych w historii zadań wynosi 2048 bajtów. |
| **Czas przechowywania historii zadań** | Historia zadania są przechowywane przez maksymalnie dwa miesiące lub maksymalnie 1000 ostatniego wykonania. | 
| **Zadania ukończone i uszkodzoną przechowywania** | Zadania ukończone i uszkodzoną są przechowywane przez 60 dni. |
||| 

