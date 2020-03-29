---
title: Urządzenie odłączanie od usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak wyrejestrować urządzenie, aby zapobiec inicjowaniu obsługi administracyjnej za pośrednictwem usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974925"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Jak odłączyć urządzenie z usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi Azure IoT Hub

Prawidłowe zarządzanie poświadczeniami urządzeń ma kluczowe znaczenie dla systemów o wysokim profilu, takich jak rozwiązania IoT. Najlepszym rozwiązaniem dla takich systemów jest mieć jasny plan, jak odwołać dostęp dla urządzeń, gdy ich poświadczenia, czy token sygnatur dostępu współdzielonego (SAS) lub certyfikat X.509, mogą zostać naruszone. 

Rejestracja w usłudze aprowizacji urządzeń umożliwia [automatyczne aprowizacji](concepts-auto-provisioning.md)urządzenia. A aprowizowana urządzenie jest taki, który został zarejestrowany w U usług IoT Hub, dzięki czemu do odbierania jego początkowego stanu [bliźniaczej reprezentacji urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) i rozpocząć raportowanie danych telemetrycznych. W tym artykule opisano sposób wyrejestrowania urządzenia z wystąpienia usługi inicjowania obsługi administracyjnej, uniemożliwiając ponowne inicjowanie obsługi administracyjnej w przyszłości.

> [!NOTE] 
> Należy pamiętać o zasadach ponawiania prób urządzeń, dla których odwołasz dostęp. Na przykład urządzenie, które ma nieskończoną zasadę ponawiania próby może stale próbować zarejestrować się w usłudze inicjowania obsługi administracyjnej. Ta sytuacja zużywa zasoby usługi i ewentualnie wpływa na wydajność.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Czarna lista urządzeń przy użyciu indywidualnego wpisu rejestracji

Rejestracje indywidualne dotyczą jednego urządzenia i mogą używać certyfikatów X.509 lub tokenów Sygnatury dostępu Współdzielonego (w rzeczywistym lub wirtualnym modułie TPM) jako mechanizmu zaświadczania. (Urządzenia, które używają tokenów Sygnatury dostępu Współdzielonego jako mechanizmu zaświadczania, mogą być aprowiowane tylko za pośrednictwem rejestracji indywidualnej). Aby wpisać na czarną listę urządzenia, które ma indywidualną rejestrację, można wyłączyć lub usunąć jego wpis rejestracji. 

Aby tymczasowo wpisać urządzenie na czarną listę, wyłączając jego wpis rejestracji: 

1. Zaloguj się do witryny Azure portal i wybierz **wszystkie zasoby** z lewego menu.
2. Na liście zasobów wybierz usługę inicjowania obsługi administracyjnej, z której chcesz z czarnej listy urządzenia.
3. W usłudze inicjowania obsługi administracyjnej wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.**
4. Wybierz wpis rejestracji dla urządzenia, które chcesz wystawić na czarną listę. 

    ![Wybierz indywidualną rejestrację](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na stronie rejestracji przewiń do dołu i wybierz pozycję **Wyłącz** dla **przełącznika Włącz wpis,** a następnie wybierz pozycję **Zapisz**.  

   ![Wyłączanie indywidualnego wpisu rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Aby trwale oznaczyć urządzenie na czarnej liście, usuwając jego wpis rejestracji:

1. Zaloguj się do witryny Azure portal i wybierz **wszystkie zasoby** z lewego menu.
2. Na liście zasobów wybierz usługę inicjowania obsługi administracyjnej, z której chcesz z czarnej listy urządzenia.
3. W usłudze inicjowania obsługi administracyjnej wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.**
4. Zaznacz pole wyboru obok wpisu rejestracji urządzenia, które chcesz wpisać na czarnej liście. 
5. Wybierz **pozycję Usuń** w górnej części okna, a następnie wybierz pozycję **Tak,** aby potwierdzić, że chcesz usunąć rejestrację. 

   ![Usuwanie indywidualnego wpisu rejestracji w portalu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Po zakończeniu procedury, powinien zostać wyświetlony wpis usunięty z listy poszczególnych rejestracji.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Czarna lista certyfikatu pośredniego lub głównego urzędu certyfikacji X.509 przy użyciu grupy rejestracji

Certyfikaty X.509 są zazwyczaj rozmieszczone w łańcuchu zaufania certyfikatów. Jeśli certyfikat na dowolnym etapie łańcucha zostanie naruszony, zaufanie zostanie przerwane. Certyfikat musi znajdować się na czarnej liście, aby uniemożliwić usłudze inicjowania obsługi administracyjnej urządzeń niższego rzędu w dowolnym łańcuchu zawierającym ten certyfikat. Aby dowiedzieć się więcej o certyfikatach X.509 i o tym, jak są one używane w usłudze inicjowania obsługi administracyjnej, zobacz [Certyfikaty X.509](./concepts-security.md#x509-certificates). 

Grupa rejestracji jest wpisem dla urządzeń, które mają wspólny mechanizm zaświadczania certyfikatów X.509 podpisanych przez ten sam pośredni lub główny urząd certyfikacji. Wpis grupy rejestracji jest skonfigurowany z certyfikatem X.509 skojarzonym z pośrednim lub głównym urzędem certyfikacji. Wpis jest również skonfigurowany z dowolnymi wartościami konfiguracji, takimi jak stan bliźniaczej reprezentacji i połączenie z koncentratorem IoT, które są współużytkowane przez urządzenia z tym certyfikatem w łańcuchu certyfikatów. Aby wpisać certyfikat na czarną listę, można wyłączyć lub usunąć jego grupę rejestracji.

Aby tymczasowo wpisać certyfikat na czarną listę przez wyłączenie jego grupy rejestracji: 

1. Zaloguj się do witryny Azure portal i wybierz **wszystkie zasoby** z lewego menu.
2. Na liście zasobów wybierz usługę inicjowania obsługi administracyjnej, z której chcesz wpisać certyfikat podpisywania na czarnej liście.
3. W usłudze inicjowania obsługi administracyjnej wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Grupy rejestracji.**
4. Wybierz grupę rejestracji przy użyciu certyfikatu, który chcesz wpisać na czarną listę.
5. Wybierz **pozycję Wyłącz** na przełączniku Włącz **wpis,** a następnie wybierz pozycję **Zapisz**.  

   ![Wyłączanie wpisu grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Aby trwale oznaczyć certyfikat na czarnej liście, usuwając jego grupę rejestracji:

1. Zaloguj się do witryny Azure portal i wybierz **wszystkie zasoby** z lewego menu.
2. Na liście zasobów wybierz usługę inicjowania obsługi administracyjnej, z której chcesz z czarnej listy urządzenia.
3. W usłudze inicjowania obsługi administracyjnej wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Grupy rejestracji.**
4. Zaznacz pole wyboru obok grupy rejestracji dla certyfikatu, który chcesz wpisać na czarną listę. 
5. Wybierz **pozycję Usuń** w górnej części okna, a następnie wybierz pozycję **Tak,** aby potwierdzić, że chcesz usunąć grupę rejestracji. 

   ![Usuwanie wpisu grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po zakończeniu procedury, powinien zostać wyświetlony wpis usunięty z listy grup rejestracji.  

> [!NOTE]
> Jeśli usuniesz grupę rejestracji dla certyfikatu, urządzenia, które mają certyfikat w łańcuchu certyfikatów, mogą nadal być w stanie zarejestrować, jeśli włączona grupa rejestracji certyfikatu głównego lub inny certyfikat pośredni wyżej w ich certyfikacie istnieje łańcuch.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Czarna lista określonych urządzeń w grupie rejestracji

Urządzenia implementujące mechanizm zaświadczania X.509 używają łańcucha certyfikatów urządzenia i klucza prywatnego do uwierzytelniania. Gdy urządzenie łączy się i uwierzytelnia z usługą inicjowania obsługi administracyjnej urządzeń, usługa najpierw wyszukuje indywidualną rejestrację, która pasuje do poświadczeń urządzenia. Następnie usługa przeszukuje grupy rejestracji, aby ustalić, czy urządzenie może być aprowizowana. Jeśli usługa znajdzie wyłączoną rejestrację indywidualną dla urządzenia, uniemożliwia to połączenie urządzenia. Usługa uniemożliwia połączenie, nawet jeśli istnieje włączona grupa rejestracji pośredniego lub głównego urzędu certyfikacji w łańcuchu certyfikatów urządzenia. 

Aby wpisać na czarną listę poszczególnych urządzeń w grupie rejestracji, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal i wybierz **wszystkie zasoby** z lewego menu.
2. Z listy zasobów wybierz usługę inicjowania obsługi administracyjnej zawierającą grupę rejestracji dla urządzenia, które chcesz wyświetlić na czarnej liście.
3. W usłudze inicjowania obsługi administracyjnej wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.**
4. Wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 
5. Na stronie **Dodaj rejestrację** wybierz **X.509** jako **mechanizm** zaświadczania dla urządzenia.

    Prześlij certyfikat urządzenia i wprowadź identyfikator urządzenia, które ma być umieszczone na czarnej liście. W przypadku certyfikatu użyj podpisanego certyfikatu jednostki końcowej zainstalowanego na urządzeniu. Urządzenie używa podpisanego certyfikatu jednostki końcowej do uwierzytelniania.

    ![Ustawianie właściwości urządzenia na czarnej liście](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Przewiń do dołu strony **Dodaj rejestrację** i wybierz pozycję **Wyłącz** na przełączniku **Włącz wprowadzanie,** a następnie wybierz pozycję **Zapisz**. 

    [![Wyłączanie urządzenia z rejestracji grupowej za pomocą wyłączonego indywidualnego wpisu rejestracji](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Po pomyślnym utworzeniu rejestracji powinna zostać wyświetlona rejestracja wyłączonego urządzenia na liście na karcie **Rejestracje indywidualne.** 

## <a name="next-steps"></a>Następne kroki

Wyrejestrowanie jest również częścią większego procesu anulowania obsługi administracyjnej. Anulowanie obsługi administracyjnej urządzenia obejmuje zarówno wyrejestrowanie z usługi inicjowania obsługi administracyjnej, jak i wyrejestrowanie z centrum IoT hub. Aby dowiedzieć się więcej o pełnym procesie, zobacz [Jak wyrejestrować urządzenia, które wcześniej były automatycznie aprowizacji](how-to-unprovision-devices.md) 

