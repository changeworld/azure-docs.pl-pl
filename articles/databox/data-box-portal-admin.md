---
title: Podręcznik administrowania usługą Azure Data Box za pomocą portalu | Microsoft Docs
description: W tym artykule opisano sposób administrowania usługą Azure Data Box w witrynie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 49c2258100e99742bcb2e22fbce7f05b69c70ef6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090726"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Administrowanie usługą Data Box w witrynie Azure Portal

W artykule opisano niektóre złożone przepływy pracy i zadania związane z zarządzaniem, które można wykonywać w usłudze Data Box. Usługą Data Box można zarządzać w witrynie Azure Portal lub za pomocą lokalnego internetowego interfejsu użytkownika. 

Ten artykuł dotyczy zadań, które można wykonywać w witrynie Azure Portal. W witrynie Azure Portal można zarządzać zamówieniami, zarządzać usługą Data Box oraz śledzić stan zamówienia na kolejnych etapach aż do ukończenia.


## <a name="cancel-an-order"></a>Anulowanie zamówienia

Może się zdarzyć, że z różnych powodów będzie konieczne anulowanie złożonego zamówienia. Zamówienie można anulować tylko przed rozpoczęciem jego przetwarzania. Po przetworzeniu zamówienia i przygotowaniu usługi Data Box nie można już anulować zamówienia. 

Aby anulować zamówienie, wykonaj poniższe czynności.

1.  Przejdź do pozycji **Przegląd > Anuluj**. 

    ![Anulowanie zamówienia — 1](media/data-box-portal-admin/cancel-order1.png)

2.  Wprowadź przyczynę anulowania zamówienia.  

    ![Anulowanie zamówienia — 2](media/data-box-portal-admin/cancel-order2.png)

3.  Po anulowaniu zamówienia jego stan w portalu zostanie zmieniony na **Anulowane**. 

## <a name="clone-an-order"></a>Klonowanie zamówienia

Klonowanie jest przydatne w pewnych sytuacjach. Załóżmy na przykład, że użytkownik skorzystał z usługi Data Box do transferu danych. W związku z wygenerowaniem większej ilości danych pojawiła się potrzeba użycia kolejnej usługi Data Box do przesłania danych na platformę Azure. W takim przypadku można po prostu sklonować to samo zamówienie.

Aby sklonować zamówienie, wykonaj następujące czynności.

1.  Przejdź do pozycji **Przegląd > Klonuj**. 

    ![Klonowanie zamówienia — 1](media/data-box-portal-admin/clone-order1.png)

2.  Wszystkie szczegóły zamówienia pozostaną takie same. Nazwa zamówienia będzie taka jak nazwa oryginalnego zamówienia z dodaną końcówką *— klon*. Zaznacz pole wyboru, aby potwierdzić zapoznanie się z informacjami dotyczącymi prywatności. Kliknij pozycję **Utwórz**.    

W ciągu kilku minut sklonowanie zamówienie zostanie utworzone i wyświetlone w portalu.


## <a name="delete-order"></a>Usuwanie zamówienia

Możesz usunąć zamówienie, gdy zostanie zakończone. Zamówienie zawiera dane osobowe, takie jak imię i nazwisko, adres i dane kontaktowe. Te dane osobowe są usuwane po usunięciu zamówienia.

Można usuwać tylko zamówienia, które zostały zakończone lub anulowane. Aby usunąć zamówienie, wykonaj następujące czynności.

1. Przejdź do sekcji **Wszystkie zasoby**. Wyszukaj zamówienie.

2. Kliknij zamówienie, które chcesz usunąć, i przejdź do sekcji **Przegląd**. Na pasku poleceń kliknij pozycję **Usuń**.

    ![Usuwanie zamówienia usługi Data Box 1](media/data-box-portal-admin/delete-order1.png)

3. Gdy pojawi się monit o potwierdzenie usunięcia zamówienia, podaj nazwę zamówienia. Kliknij polecenie **Usuń**.

## <a name="download-shipping-label"></a>Pobieranie etykiety wysyłkowej

Jeśli wyświetlacz E-ink urządzenia Data Box nie działa i nie wyświetla zwrotnej etykiety wysyłkowej, może być konieczne pobranie etykiety wysyłkowej. 

Aby pobrać etykietę wysyłkową, wykonaj następujące czynności.
1.  Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**. Ta opcja jest dostępna tylko po wysłaniu urządzenia. 

    ![Pobieranie etykiety wysyłkowej](media/data-box-portal-admin/download-shipping-label.png)

2.  To spowoduje pobranie następującej zwrotnej etykiety wysyłkowej. Zapisz i wydrukuj etykietę. Złóż etykietę i włóż ją do przezroczystej koszulki na urządzeniu. Upewnij się, że etykieta jest widoczna. Usuń wszystkie znajdujące się na urządzeniu nalepki z poprzedniej wysyłki.

    ![Przykładowa etykieta wysyłkowa](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Edytowanie adresu wysyłkowego

Może się zdarzyć, że po złożeniu zamówienia konieczne będzie edytowanie adresu wysyłkowego. Jest to możliwe tylko przed wysłaniem urządzenia. Po wysłaniu urządzenia ta opcja nie jest już dostępna.

Aby edytować zamówienie, wykonaj następujące czynności.

1. Przejdź do pozycji **Szczegóły zamówienia > Edytuj adres wysyłkowy**.

    ![Edytowanie adresu wysyłkowego — 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Przeprowadź edycję adresu wysyłkowego i sprawdź jego poprawność, a następnie zapisz zmiany.

    ![Edytowanie adresu wysyłkowego — 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Edytowanie szczegółów powiadomienia

Może być konieczna zmiana danych użytkowników, którzy mają otrzymywać wiadomości e-mail z powiadomieniami o stanie zamówienia. Na przykład określony użytkownik może potrzebować informacji o dostarczeniu lub odebraniu urządzenia. Inny użytkownik może potrzebować informacji o zakończeniu kopiowania danych, aby móc sprawdzić, czy dane znajdują się już na koncie usługi Azure Storage, a następnie usunąć je ze źródła. W takich przypadkach można edytować szczegóły powiadomień.

Aby edytować szczegóły powiadomień, wykonaj następujące czynności.

1. Przejdź do pozycji **Szczegóły zamówienia > Edytowanie szczegółów powiadomienia**.

    ![Edytowanie szczegółów powiadomienia — 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Możesz teraz edytować szczegóły powiadomień. Następnie zapisz zmiany.
 
    ![Edytowanie szczegółów powiadomienia — 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="view-order-status"></a>Wyświetlanie stanu zamówienia

Po zmianie stanu urządzenia w portalu otrzymasz powiadomienie pocztą e-mail.

|Stan zamówienia |Opis |
|---------|---------|
|Zamówione     | Pomyślnie złożono zamówienie. <br>Jeśli urządzenie jest dostępne, firma Microsoft identyfikuje i przygotowuje urządzenie do wysłania. <br> Jeśli urządzenie nie jest dostępne natychmiast, zamówienie zostanie przetworzone, gdy urządzenie stanie się dostępne. Przetworzenie zamówienia może potrwać od kilku dni do kilku miesięcy. Jeśli zamówienia nie można zrealizować w ciągu 90 dni, zostanie ono anulowane, a Ty otrzymasz powiadomienie.         |
|Przetworzone     | Zakończono przetwarzanie zamówienia. Zgodnie z Twoim zamówieniem urządzenie jest gotowe do wysyłki w centrum danych.         |
|Wysłane     | Zamówienie zostało wysłane. Skorzystaj z identyfikatora śledzenia wyświetlanego w zamówienia w portalu, aby śledzić wysyłkę.        |
|Dostarczono     | Wysyłka została dostarczona na adres wskazany w zamówieniu.        |
|Pobrane     |Twoja wysyłka zwrotna została pobrana i zeskanowana przez operatora.         |
|Odebrano     | Twoje urządzenie zostało odebrane i zeskanowane w centrum danych platformy Azure. <br> Po sprawdzeniu wysyłki rozpocznie się przekazywanie urządzenia.      |
|Kopiowanie danych     | Kopiowanie danych jest w toku. Śledź postęp kopiowania w ramach Twojego zamówienia w witrynie Azure Portal. <br> Poczekaj na zakończenie kopiowania danych. |
|Zakończone       |Zamówienie zostało pomyślnie zrealizowane.<br> Przed usunięciem danych lokalnych z serwerów upewnij się, że Twoje dane znajdują się na platformie Azure.         |
|Zakończone z błędami| Kopiowanie danych zostało ukończone, ale podczas kopiowania wystąpiły błędy. <br> Przejrzyj dzienniki kopiowania, używając ścieżki wskazanej w witrynie Azure Portal.   |
|Anulowane            |Twoje zamówienie zostało anulowane. <br> Zamówienie mogło zostać anulowane przez Ciebie lub, w przypadku wystąpienia błędu, przez usługę. Jeśli zamówienia nie można zrealizować w ciągu 90 dni, także zostanie ono anulowane, a Ty otrzymasz powiadomienie.     |
|Czyszczenie | Dane na dyskach urządzenia zostaną usunięte. Czyszczenie urządzenia uważa się za ukończone, gdy raport dziennika zamówienia jest dostępny w witrynie Azure Portal.|



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z usługą Data Box](data-box-faq.md).
