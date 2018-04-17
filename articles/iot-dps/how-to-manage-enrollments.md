---
title: Zarządzanie rejestracji urządzenia z portalu Azure | Dokumentacja firmy Microsoft
description: Jak zarządzać rejestracji urządzeń usługi punktu dystrybucji w portalu Azure
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 55486d9a37968351f5313c708e9ef26e5b89063c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak zarządzać rejestracji urządzenia z portalu Azure

A *rejestracji urządzeń* tworzy rekord jednego urządzenia lub grupy urządzeń, które w pewnym momencie mogą rejestrować w usłudze Azure IoT Hub urządzenia inicjowania obsługi administracyjnej. Rekord rejestracji zawiera początkowej żądanej konfiguracji dla urządzeń w ramach rejestracji, łącznie z żądaną Centrum IoT. W tym artykule przedstawiono sposób zarządzania rejestracji urządzeń w usłudze udostępniania.


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

Istnieją dwa sposoby mogą rejestrować urządzenia w usłudze inicjowania obsługi administracyjnej:

* **Grupy rejestracji** wpis dla grupy urządzeń, które współużytkują wspólnego mechanizmu zaświadczania certyfikatów X.509, podpisane przez tego samego certyfikatu podpisywania, który może być [certyfikat główny](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatu pośredniego](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), użytego do utworzenia certyfikatu urządzenia na urządzeniu fizycznym. Firma Microsoft zaleca używanie grupy rejestracji dużej liczby urządzeń, których udostępnianie wymaganą konfiguracją początkową lub urządzeń wszystkich przejść do tej samej dzierżawy. Należy pamiętać, że tylko mogą rejestrować urządzenia, które korzystają z mechanizmu zaświadczania X.509 jako *grup rejestracji*. 

    Można utworzyć grupę rejestracji w portalu dla grupy urządzeń, wykonując następujące czynności:

    1. Zaloguj się do portalu Azure, a następnie kliknij przycisk **wszystkie zasoby** z menu po lewej stronie.  
    2. Kliknij usługę Inicjowanie obsługi administracyjnej urządzeń, aby rejestrowanie urządzenia w celu z listy zasobów.  
    3. W usłudze inicjowania obsługi administracyjnej:  
       a. Kliknij przycisk **Zarządzanie rejestracji**, a następnie wybierz pozycję **grup rejestracji** kartę.  
       b. Kliknij przycisk **Dodaj** na górze.  
       c. Gdy pojawi się na panelu "Dodaj grupę rejestracji", wprowadź informacje dotyczące rejestracji pozycji listy.  **Nazwa grupy** jest wymagana. Również wybrać opcję "Urzędu certyfikacji lub pośredniego" dla **typ certyfikatu**i przekazać głównego **podstawowego certyfikatu** dla grupy urządzeń.  
       d. Kliknij pozycję **Zapisz**. O pomyślnym utworzeniu grupy rejestracji, powinna zostać wyświetlona nazwa grupy są wyświetlane w obszarze **grup rejestracji** kartę.  

       [![Grupa rejestracji w portalu](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Poszczególnych rejestracji** wpis dla pojedynczego urządzenia, które mogą zarejestrować. Poszczególne rejestracji może używać albo x509 certyfikatów lub SAS tokeny (fizycznych lub wirtualnych modułu TPM) jako mechanizmy zaświadczania. Zalecamy używanie poszczególnych rejestracji dla urządzeń, które wymagają szczególnej konfiguracji początkowej lub dla urządzeń, których można używać tylko tokeny sygnatury dostępu Współdzielonego, za pomocą modułu TPM lub wirtualne modułu TPM jako mechanizm zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Można utworzyć indywidualne rejestracji w portalu, wykonując następujące czynności:

    1. Zaloguj się do portalu Azure, a następnie kliknij przycisk **wszystkie zasoby** z menu po lewej stronie.
    2. Kliknij usługę Inicjowanie obsługi administracyjnej urządzeń, aby rejestrowanie urządzenia w celu z listy zasobów.
    3. W usłudze inicjowania obsługi administracyjnej:  
       a. Kliknij przycisk **Zarządzanie rejestracji**, a następnie wybierz pozycję **poszczególnych rejestracji** kartę.  
       b. Kliknij przycisk **Dodaj** na górze.   
       c. Gdy pojawi się panel "Rejestrowanie Dodaj", wprowadź informacje dotyczące rejestracji pozycji listy. Najpierw wybierz poświadczenie **mechanizmu** dla urządzenia (X.509 lub modułu TPM). Poświadczenie X.509 wymagane jest wysłanie liścia **podstawowego certyfikatu** dla urządzenia. Moduł TPM wymaga wprowadzenia **zaświadczenie klucza** i **identyfikator rejestracji** dla urządzenia.  
       d. Kliknij pozycję **Zapisz**. O pomyślnym utworzeniu grupy rejestracji, powinien zostać wyświetlony urządzenia są wyświetlane w obszarze **poszczególnych rejestracji** kartę.  

       [![Poszczególne rejestracji w portalu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizacja wpisu rejestracji
Można zaktualizować istniejący wpis rejestracji w portalu, wykonując następujące czynności:

1. Otwórz usługi Inicjowanie obsługi administracyjnej urządzeń w portalu Azure i kliknij przycisk **Zarządzanie rejestracji**. 
2. Przejdź do wpisu rejestracji, który chcesz zmodyfikować. Kliknij wpis, który otwiera podsumowanie informacji o rejestracji z urządzeń. 
3. Na tej stronie można zmodyfikować elementów innych niż typ zabezpieczeń i poświadczeń, takich jak Centrum IoT urządzenia powinna być połączona, a także identyfikator urządzenia. Stan dwie początkowej urządzenia może także modyfikować. 
4. Po ukończeniu kliknij przycisk **zapisać** można zaktualizować informacji o rejestracji urządzenia. 

    ![Aktualizacja rejestracji w portalu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Usuwanie rejestracji urządzeń
W przypadkach, gdy z urządzenia nie muszą być przygotowana do dowolnego Centrum IoT możesz usunąć wpis pokrewne rejestracji w portalu, wykonując następujące czynności:

1. Otwórz usługi Inicjowanie obsługi administracyjnej urządzeń w portalu Azure i kliknij przycisk **Zarządzanie rejestracji**. 
2. Przejdź do i wybierz wpis rejestracji, który ma zostać usunięty. 
3. Kliknij przycisk **usunąć** znajdujący się u góry, a następnie wybierz **tak** po wyświetleniu monitu o potwierdzenie. 
5. Po zakończeniu działania zostanie wyświetlony wpis usunięty z listy rejestracji urządzeń. 
 
    ![Usuwanie rejestracji w portalu](./media/how-to-manage-enrollments/remove-enrollment.png)


