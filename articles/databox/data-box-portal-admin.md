---
title: Podręcznik administrowania usługą Azure Data Box za pomocą portalu | Microsoft Docs
description: W tym artykule opisano sposób administrowania usługą Azure Data Box w witrynie Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: 1b228a66f2d59b3ff252df266783f7bd5d27139e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645443"
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


## <a name="download-order-history"></a>Pobieranie historii zamówienia

Po zakończeniu zamówienia dotyczącego urządzenia Data Box dane na dyskach urządzenia są wymazywane. Po zakończeniu czyszczenia urządzenia możesz pobrać historię zamówienia w witrynie Azure Portal.

Aby pobrać historię zamówienia, wykonaj poniższe kroki.

1. W swoim zamówieniu dotyczącym urządzenia Data Box przejdź na kartę **Przegląd**. Upewnij się, że zamówienie jest zakończone. Jeśli zamówienie jest zakończone i czyszczenie urządzenia zostało ukończone, przejdź do pozycji **Szczegóły zamówienia**. Opcja **Pobieranie historii zamówienia** jest dostępna.

    ![Pobieranie historii zamówienia](media/data-box-portal-admin/download-order-history-1.png)

2. Kliknij pozycję **Pobierz historię zamówienia**. W pobranej historii zostanie wyświetlony rekord dzienników śledzenia przewoźnika. Po przewinięciu do dołu tego dziennika możesz zobaczyć linki do następujących obiektów:
    
    - **Dzienniki kopiowania** — lista plików, które wygenerowały błędy podczas kopiowania danych z urządzenia Data Box do Twojego konta usługi Azure Storage.
    - **Dzienniki inspekcji** — zawierają informacje o włączeniu i dostępie do udziału na urządzeniu Data Box, gdy znajduje się poza centrum danych platformy Azure.
    - **Pliki BOM** — uzyskaj listę plików (nazywaną także manifestem plików), które można pobrać podczas **przygotowania do wysłania**, zawierająca nazwy plików, rozmiary plików i sumy kontrolne plików.

        ```
        -------------------------------
        Microsoft Data Box Order Report
        -------------------------------
        
        Name                                               : eastusdryrun                                      
        StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
        DeviceType                                         : ImolaPod                                          
        
        -------------------
        Data Box Activities
        -------------------
        
        Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
        9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
        9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
        9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
        9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
        9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
        9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
        9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
        9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
        9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
        9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
        9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
        9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
        9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
        9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
        9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
        9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
        9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
        9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
        9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
        9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
        ----------------------
        Data Box Job Log Links
        ----------------------
        
        Account Name         : eastusdryrun                                         
        Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
        Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
        BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
        ```
Następnie możesz przejść do swojego konta magazynu i wyświetlić dzienniki kopiowania.

![Dzienniki w ramach kont magazynu](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Możesz również wyświetlić łańcuch dzienników nadzoru, które zawierają dzienniki inspekcji oraz pliki BOM.

![Dzienniki w ramach kont magazynu](media/data-box-portal-admin/logs-in-storage-acct-1.png)

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
|Czyszczenie | Dane na dyskach urządzenia zostaną usunięte. Czyszczenie urządzenia uważa się za ukończone, gdy historia zamówienia jest dostępna do pobrania w witrynie Azure Portal.|



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z usługą Data Box](data-box-faq.md).
