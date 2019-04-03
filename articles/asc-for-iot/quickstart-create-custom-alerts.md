---
title: Utwórz niestandardowe alerty dla Centrum zabezpieczeń Azure dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Utwórz i przypisz niestandardowe alerty Centrum zabezpieczeń Azure dla IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: d793b105e6d73c98739cd05d6e19a218413d7813
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861999"
---
# <a name="quickstart-create-custom-alerts"></a>Szybki start: Utwórz niestandardowe alerty

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Korzystanie z grup zabezpieczeń niestandardowe i alerty, umożliwiają pełne wykorzystywanie zalet informacji o zabezpieczeniach end-to-end i wiedzy podzielonych na kategorie urządzeń, aby zapewnić większe bezpieczeństwo w rozwiązania IoT. 

## <a name="why-use-custom-alerts"></a>Dlaczego warto używać niestandardowe alerty? 

Dostęp do najlepszych urządzeń IoT.

Dla klientów, którzy w pełni zrozumieć ich zachowanie oczekiwanego urządzenia Azure Security Center (ASC) dla IoT umożliwia tłumaczenie tę wiedzę do zasad zachowania urządzenia i alertu na wszelkie odchylenia od oczekiwano normalne zachowanie.

## <a name="security-groups"></a>Grupy zabezpieczeń

Grupy zabezpieczeń umożliwiają definiowanie grup logicznych urządzeń i zarządzanie ich stanu zabezpieczeń w sposób scentralizowany.

Te grupy mogą reprezentować urządzenia przy użyciu określonego sprzętu wdrażane w określonej lokalizacji lub jakakolwiek inna grupa odpowiedni do konkretnych potrzeb.

Grupy zabezpieczeń są definiowane przez właściwości tagu bliźniaczej reprezentacji modułu zabezpieczeń o nazwie **SecurityGroup**. Zmień wartość tej właściwości można zmienić grupy zabezpieczeń urządzenia.  

Domyślnie każde rozwiązanie IoT w usłudze IoT Hub ma jedną grupę zabezpieczeń o nazwie **domyślne**.

Użyj grup zabezpieczeń do grupowania urządzeń w kategorie logiczne. Po utworzeniu grupy, należy je przypisać do niestandardowe alerty, co pozwala na najbardziej efektywne rozwiązanie end-to-end. 

## <a name="customize-an-alert"></a>Dostosowywanie alertu

1. Otwórz Centrum IoT Hub. 
2. Wybierz **zabezpieczeń**, a następnie wybierz **niestandardowe alerty**. 
3. Wybierz grupy zabezpieczeń, którą chcesz zastosować dostosowania do. 
4. Kliknij przycisk **Dodaj alert niestandardowy**
5. Wprowadź nazwę alertu (należy zauważyć, że po utworzeniu nie można zmienić nazwy alertu). 
6. Wybierz niestandardowe zachowanie alertów z listy rozwijanej. 
7. Kliknij pozycję Edytuj wymaganych właściwości **OK**.
8. Upewnij się, że kliknij **ZAPISZ**. Bez zapisywania nowego alertu, alert zostanie usunięty po zamknięciu Centrum IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alerty, możliwe do dostosowania

Poniższa tabela zawiera podsumowanie alertów, które są możliwe do dostosowania.

| Ważność | Name (Nazwa)                                                                                                    | Źródło danych | Opis                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Małe      | Alert niestandardowy — liczba komunikatów w protokołu AMQP z urządzeń w chmurze nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość w chmurze i komunikaty z urządzenia (protokół AMQP) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Alert niestandardowy — liczba odrzuconych w chmurze i komunikaty z urządzenia w protokołu AMQP, nie jest dozwolony zakres | Usługa IoT Hub     | Ilość w chmurze i komunikatów z urządzeń (protokół AMQP), które zostały odrzucone przez urządzenie w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres |
| Małe      | Alert niestandardowy — liczba urządzeń do chmury wiadomości protokołu AMQP, nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość urządzenia komunikatów z chmury (protokół AMQP) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Wywołuje niestandardowy Alert — liczba metody bezpośredniej nie jest dozwolony zakres                              | Usługa IoT Hub     | Ilość metody bezpośredniej wywołuje w oknie nie znajduje się w skonfigurowanym dozwolony zakres czasu                                                     |
| Małe      | Alert niestandardowy — liczba operacji przekazywania plików nie jest dozwolony zakres                                       | Usługa IoT Hub     | Ilość przekazywanych plików w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                                              |
| Małe      | Alert niestandardowy — liczba komunikatów w protokole HTTP z urządzeń w chmurze nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość w chmurze i komunikaty z urządzenia (protokół HTTP) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Alert niestandardowy — liczba odrzuconych chmury do urządzenia komunikaty w protokole HTTP nie jest dozwolony zakres | Usługa IoT Hub     | Ilość w chmurze i komunikatów z urządzeń (protokół HTTP), które zostały odrzucone przez urządzenie w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres |
| Małe      | Alert niestandardowy — liczba urządzeń do chmury komunikaty w protokole HTTP nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość urządzenia komunikatów z chmury (protokół HTTP) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Alert niestandardowy — liczba komunikatów z urządzeń w protokołu MQTT w chmurze nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość w chmurze i komunikaty z urządzenia (protokołu MQTT) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Alert niestandardowy — liczba odrzuconych w chmurze i komunikaty z urządzenia w protokołu MQTT, nie jest dozwolony zakres | Usługa IoT Hub     | Ilość w chmurze i komunikatów z urządzeń (protokołu MQTT), które zostały odrzucone przez urządzenie w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres |
| Małe      | Alert niestandardowy — liczba urządzeń do chmury wiadomości protokołu MQTT, nie jest dozwolony zakres          | Usługa IoT Hub     | Ilość urządzenia komunikatów z chmury (protokołu MQTT) w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                  |
| Małe      | Alert niestandardowy — liczba powoduje usunięcie kolejki poleceń nie jest dozwolony zakres                               | Usługa IoT Hub     | Wielkość kolejki poleceń Przeczyszcza w oknie nie znajduje się w skonfigurowanym dozwolony zakres czasu                                                      |
| Małe      | Alert niestandardowy — liczba aktualizacji bliźniaczej reprezentacji nie jest dozwolony zakres                                       | Usługa IoT Hub     | Liczba aktualizacji bliźniaczej reprezentacji w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                                              |
| Małe      | Alert niestandardowy — liczba operacji nieautoryzowanych nie jest dozwolony zakres                            | Usługa IoT Hub     | Ilość nieautoryzowanych operacji w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                                   |
| Małe      | Alert niestandardowy — liczba aktywnych połączeń nie jest dozwolony zakres                                        | Agent       | Liczba aktywnych połączeń w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                                        |
| Małe      | Utworzono Alert niestandardowy — połączenie wychodzące do adresu ip, który nie jest dozwolona                              | Agent       | Utworzono połączenie wychodzące do adresu ip, który nie jest dozwolona                                                                                  |
| Małe      | Alert niestandardowy — liczba nieudanych prób logowania lokalnego nie jest dozwolony zakres                                | Agent       | Liczba nieudanych prób logowania lokalnego, w przedziale czasu nie znajduje się w skonfigurowanym dozwolony zakres                                                       |
| Małe      | Alert niestandardowy — logowania użytkownika, który jest niedozwolony                                                      | Agent       | Użytkownika lokalnego, która nie jest dozwolona zalogowany do urządzenia                                                                                        |
| Małe      | Alert niestandardowy — wykonanie procesu, który nie jest dozwolona                                               | Agent       | Wykonano procesu, który nie jest dozwolone na urządzeniu |          |

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdróż agenta zabezpieczeń](how-to-deploy-agent.md)
