---
title: Zarządzanie rejestracjami urządzeń przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Jak zarządzać rejestracji urządzeń w usłudze Device Provisioning w witrynie Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51b072bfd0827528a5504133dff8c1cdd7a7ca86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122779"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak zarządzać rejestracjami urządzeń przy użyciu witryny Azure Portal

A *rejestracji urządzeń* tworzy rekord pojedynczego urządzenia lub grupy urządzeń, które mogą w pewnym momencie zarejestrować w usłudze Azure IoT Hub Device Provisioning Service. Rekord rejestracji zawiera odpowiednią konfigurację wstępną dla urządzeń w ramach rejestracji, w tym wymagane Centrum IoT hub. Ten artykuł pokazuje, jak zarządzanie rejestracjami urządzeń w Twojej usługi aprowizacji.


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

Istnieją dwa sposoby, które mogą rejestrować urządzenia z usługą aprowizacji:

* **Grupy rejestracji** wpis dla grupy urządzeń, które współużytkują wspólne mechanizm zaświadczania certyfikatów X.509, podpisane przez ten sam certyfikat podpisywania, który może być [certyfikat główny](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [pośredniego certyfikatu](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), który jest używany w celu wygenerowania certyfikatu urządzenia na urządzeniu fizycznym. Zalecamy używanie grupę rejestracji dużą liczbę urządzeń, które mają wybranej konfiguracji początkowej lub urządzeń wszystkich przejść do tej samej dzierżawy. Należy pamiętać, że możesz tylko rejestrować urządzenia, które korzystają z mechanizmu zaświadczania X.509 jako *grup rejestracji*. 

    Można utworzyć grupę rejestracji w portalu dla grupy urządzeń wykonując następujące czynności:

  1. Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **wszystkie zasoby** z menu po lewej stronie.  
  2. Kliknij opcję usługi Device Provisioning, aby zarejestrować urządzenia w celu z listy zasobów.  
  3. W usłudze aprowizacji:  
     a. Kliknij przycisk **Zarządzanie rejestracjami**, a następnie wybierz **grup rejestracji** kartę.  
     b. Kliknij przycisk **Dodaj** na górze.  
     c. Gdy zostanie wyświetlony panel "Dodaj grupę rejestracji", wprowadź informacje dla wpisu listy rejestracji.  **Nazwa grupy** jest wymagana. Również wybrać opcję "Urzędu certyfikacji lub pośredniego" dla **typ certyfikatu**i przekaż głównego **podstawowy certyfikat** dla grupy urządzeń.  
     d. Kliknij pozycję **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji, powinien zostać wyświetlony nazwę grupy są wyświetlane w obszarze **grup rejestracji** kartę.  

     [![Grupy rejestracji w portalu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Rejestracji indywidualnej** wpis dla pojedynczego urządzenia, które może zarejestrować. Rejestracje indywidualne mogą używać dowolnego x509 certyfikatów lub tokenów SAS (z fizycznym lub wirtualnym module TPM) jako mechanizmów zaświadczania. Firma Microsoft zaleca używanie rejestracji indywidualnych w przypadku urządzeń, które wymagają unikatowej konfiguracji początkowej lub urządzeń, których można używać tylko tokenów SAS za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Możesz utworzyć rejestrację indywidualną w portalu wykonując następujące czynności:

    1. Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **wszystkie zasoby** z menu po lewej stronie.
    1. Kliknij opcję usługi Device Provisioning, aby zarejestrować urządzenia w celu z listy zasobów.
    1. W usłudze aprowizacji:  
       a. Kliknij przycisk **Zarządzanie rejestracjami**, a następnie wybierz **rejestracje indywidualne** kartę.  
       b. Kliknij przycisk **Dodaj** na górze.   
       c. Gdy zostanie wyświetlony panel "Rejestrowanie Dodaj", wprowadź informacje dla wpisu listy rejestracji. Najpierw wybierz poświadczenie **mechanizm** dla urządzenia (X.509 lub moduł TPM). Zaświadczenia X.509 wymaga przekazania liścia **podstawowy certyfikat** dla tego urządzenia. Moduł TPM wymaga wprowadzenia **klucza zaświadczania** i **identyfikator rejestracji** dla tego urządzenia.  
       d. Kliknij pozycję **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji, powinien zostać wyświetlony urządzenia są wyświetlane w obszarze **rejestracje indywidualne** kartę.  

       [![Rejestracja indywidualna w portalu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Zaktualizuj wpis rejestracji
Możesz zaktualizować istniejący wpis rejestracji w portalu wykonując następujące czynności:

1. Otwórz swoją usługę Device Provisioning w witrynie Azure portal, a następnie kliknij przycisk **Zarządzanie rejestracjami**. 
1. Przejdź do wpisu rejestracji, który chcesz zmodyfikować. Kliknij wpis, który otwiera podsumowanie informacji dotyczących rejestrowania urządzeń z systemem. 
1. Na tej stronie można modyfikować elementów innych niż typ zabezpieczeń i poświadczeń, takich jak urządzenia usługi IoT hub powinny być powiązane, a także identyfikator urządzenia. Może także modyfikować stan bliźniaczej reprezentacji urządzenia początkowej. 
1. Po zakończeniu kliknij przycisk **Zapisz** można zaktualizować usługi rejestracji urządzeń. 

    ![Aktualizowanie rejestracji w portalu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Usuwanie rejestracji urządzenia
W przypadkach, w którym tego urządzenia nie muszą być przygotowana do dowolnej usługi IoT hub można usunąć wpisu rejestracji powiązane w portalu wykonując następujące czynności:

1. Otwórz swoją usługę Device Provisioning w witrynie Azure portal, a następnie kliknij przycisk **Zarządzanie rejestracjami**. 
1. Przejdź do, a następnie wybierz wpis rejestracji, który chcesz usunąć. 
1. Kliknij przycisk **Usuń** znajdujący się u góry, a następnie wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. 
1. Po ukończeniu akcji pojawi się wpis usunięty z listy rejestracji urządzeń. 
 
    ![Usuwanie rejestracji w portalu](./media/how-to-manage-enrollments/remove-enrollment.png)


