---
title: Zarządzanie rejestracjami urządzeń dla usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub w witrynie Azure portal
description: Jak zarządzać rejestracjami urządzeń dla usługi inicjowania obsługi urządzeń (DPS) w witrynie Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974942"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak zarządzać rejestracjami urządzeń za pomocą usługi Azure Portal

*Rejestracja urządzenia* tworzy rekord pojedynczego urządzenia lub grupy urządzeń, które w pewnym momencie mogą zarejestrować się w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub. Rekord rejestracji zawiera początkową żądaną konfigurację dla urządzeń w ramach tej rejestracji, w tym żądaną ioT hub. W tym artykule pokazano, jak zarządzać rejestracjami urządzeń dla usługi inicjowania obsługi administracyjnej.


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzenia

Istnieją dwa sposoby rejestrowania urządzeń za pomocą usługi inicjowania obsługi administracyjnej:

* **Grupa Rejestracji** to wpis dla grupy urządzeń, które mają wspólny mechanizm zaświadczania certyfikatów X.509, podpisany tym samym certyfikatem podpisywania, który może być [certyfikatem głównym](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatem pośrednim,](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)używanym do tworzenia certyfikatu urządzenia na urządzeniu fizycznym. Zaleca się używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową lub dla urządzeń przechodzących do tej samej dzierżawy. Należy zauważyć, że jako grupy rejestracji można rejestrować tylko urządzenia *korzystające*z mechanizmu zaświadczania X.509. 

    Grupę rejestracji w portalu można utworzyć dla grupy urządzeń, wykonując następujące czynności:

  1. Zaloguj się do witryny Azure portal i kliknij pozycję **Wszystkie zasoby** z menu po lewej stronie.  
  1. Kliknij usługę inicjowania obsługi administracyjnej urządzeń, do której chcesz zarejestrować urządzenie, z listy zasobów.  
  1. W usłudze aprowizacji:  
     a. Kliknij **pozycję Zarządzaj rejestracjami**, a następnie wybierz kartę **Grupy rejestracji.**  
     b. Kliknij przycisk **Dodaj** na górze.  
     d. Po wyświetleniu panelu "Dodaj grupę rejestracji" wprowadź informacje dotyczące wpisu listy rejestracji.  Wymagana jest **nazwa grupy.** Wybierz również "CA lub Intermediate" dla **typu certyfikatu**i przekaż główny **certyfikat podstawowy** dla grupy urządzeń.  
     d. Kliknij przycisk **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji nazwa grupy powinna być wyświetlana na karcie **Grupy rejestracji.**  

     [![Grupa rejestracji w portalu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Rejestracja indywidualna** to wpis dla jednego urządzenia, które może się zarejestrować. Poszczególne rejestracje mogą używać certyfikatów x509 lub tokenów Sygnatury dostępu Współdzielonego (z fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Zaleca się używanie poszczególnych rejestracji dla urządzeń, które wymagają unikatowych konfiguracji początkowych lub dla urządzeń, które mogą używać tokenów sygnatury dostępu Współdzielonego tylko za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Rejestrację indywidualną można utworzyć w portalu, wykonując następujące czynności:

    1. Zaloguj się do witryny Azure portal i kliknij pozycję **Wszystkie zasoby** z menu po lewej stronie.
    1. Kliknij usługę inicjowania obsługi administracyjnej urządzeń, do której chcesz zarejestrować urządzenie, z listy zasobów.
    1. W usłudze aprowizacji:  
       a. Kliknij **pozycję Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.**  
       b. Kliknij przycisk **Dodaj** na górze.   
       d. Po wyświetleniu panelu "Dodaj rejestrację" wprowadź informacje dotyczące wpisu listy rejestracji. Najpierw wybierz **mechanizm** zaświadczania dla urządzenia (X.509 lub TPM). Zaświadczenie X.509 wymaga przesłania **certyfikatu podstawowego** typu liść dla urządzenia. Moduł TPM wymaga wprowadzenia **klucza zaświadczania** i **identyfikatora rejestracji** urządzenia.  
       d. Kliknij przycisk **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji urządzenie powinno być widoczne na karcie **Rejestracje indywidualne.**  

       [![Rejestracja indywidualna w portalu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizowanie wpisu rejestracji
Istniejący wpis rejestracji w portalu można zaktualizować, wykonując następujące czynności:

1. Otwórz usługę inicjowania obsługi urządzeń w witrynie Azure portal i kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do wpisu rejestracji, który chcesz zmodyfikować. Kliknij wpis, który otworzy podsumowanie informacji o rejestracji urządzenia. 
1. Na tej stronie można zmodyfikować elementy inne niż typ zabezpieczeń i poświadczenia, takie jak centrum IoT, z którego urządzenie powinno być połączone, a także identyfikator urządzenia. Można również zmodyfikować początkowy stan bliźniaczej reprezentacji urządzenia. 
1. Po zakończeniu kliknij przycisk **Zapisz,** aby zaktualizować rejestrację urządzenia. 

    ![Aktualizowanie rejestracji w portalu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Usuwanie rejestracji urządzenia
W przypadkach, gdy urządzenia nie muszą być aprowiedny do dowolnego centrum IoT hub, można usunąć powiązany wpis rejestracji w portalu, wykonując następujące kroki:

1. Otwórz usługę inicjowania obsługi urządzeń w witrynie Azure portal i kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do wpisu rejestracji, który chcesz usunąć, i wybierz go. 
1. Kliknij przycisk **Usuń** u góry, a następnie wybierz pozycję **Tak** po wyświetleniu monitu o potwierdzenie. 
1. Po zakończeniu akcji zostanie usunięty wpis z listy rejestracji urządzeń. 
 
    ![Usuwanie rejestracji w portalu](./media/how-to-manage-enrollments/remove-enrollment.png)


