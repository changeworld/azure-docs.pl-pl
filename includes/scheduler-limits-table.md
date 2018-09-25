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
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006495"
---
W poniższej tabeli opisano każdy z głównych przydziały, limity, wartości domyślne i ograniczenia w usłudze Azure Scheduler.

| Zasób | Opis elementu limit |
| -------- | ----------------- |
| **Rozmiar zadania** | Maksymalny rozmiar zadania jest 16 tys. Jeśli operacja PATCH lub PUT powoduje przekracza ten limit rozmiaru zadania, 400 Niewłaściwe żądanie zwróciła kod stanu. | 
| **Kolekcje zadań** | Maksymalna liczba kolekcji zadań na subskrypcję platformy Azure wynosi 200 000. | 
| **Liczba zadań na kolekcję** | Domyślnie maksymalna liczba zadań jest pięć zadań w kolekcji zadań w warstwie bezpłatna i 50 zadań w kolekcji standardowych zadań. <p>Możesz zmienić maksymalną liczbę zadań na kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone do wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć większą liczbę zadań niż zadania maksymalny limit przydziału, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. | 
| **Czas na czas rozpoczęcia** | Wartość maksymalna "przyszedł czas na czas" to 18 miesięcy. |
| **Zakres cyklu** | Zakres maksymalna wartość cyklu jest 18 miesięcy. | 
| **Częstotliwość** | Domyślnie przydział maksymalnej częstotliwości jest jedną godzinę w kolekcji zadań w warstwie bezpłatna i jedną minutę w kolekcji standardowych zadań. <p>Można wprowadzić maksymalną częstotliwość na kolekcji zadań niższa niż wartość maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone do wartość ustawioną na kolekcji zadań. Jeśli spróbujesz utworzyć zadanie z częstotliwością wyższa niż maksymalna częstotliwość na kolekcji zadań, żądanie kończy się niepowodzeniem z kodem stanu 409 konflikt. | 
| **Rozmiar treści** | Rozmiar maksymalny treść żądania jest 8192 znaków. |
| **Rozmiar adresu URL żądania** | Maksymalny rozmiar, w adresie URL żądania jest 2048 znaków. |
| **Liczba w nagłówku** | Liczba maksymalna nagłówka to 50 nagłówków. | 
| **Rozmiar nagłówka agregacji** | Rozmiar maksymalny łączny nagłówka wynosi 4096 znaków. |
| **limit czasu** | Limit czasu żądania jest statyczny (nie można konfigurować) i wynosi 60 sekund dla akcji HTTP. W przypadku dłużej uruchomionej operacji wykonaj asynchroniczne protokołów HTTP. Na przykład niezwłocznie zawrócone 202, ale nadal działa w tle. | 
| **Historia zadania** | Treść odpowiedzi maksymalna przechowywanych w historii zadań wynosi 2048 bajtów. |
| **Czas przechowywania historii zadań** | Historia zadania są przechowywane przez maksymalnie 2 miesiące lub maksymalnie 1000 ostatniego wykonania. | 
| **Zadania ukończone i uszkodzoną przechowywania** | Zadania ukończone i uszkodzoną są przechowywane przez 60 dni. |
||| 

