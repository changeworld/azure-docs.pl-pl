---
title: Jak disenroll urządzenia z usługi Azure IoT Hub Device Provisioning Service
description: Jak disenroll urządzenia, aby zapobiec obsługę administracyjną przy użyciu usługi Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0dadf0ec248dac01e5cc65779004477bf4afc823
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113585"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Jak disenroll urządzenia z usługi Azure IoT Hub Device Provisioning Service

Właściwe zarządzanie poświadczenia urządzenia ma kluczowe znaczenie podczas ważne systemów, takich jak rozwiązania IoT. Najlepszym rozwiązaniem dla takich systemów jest zapewnienie Wyczyść plan sposób odwołać dostępu do urządzeń podczas swoje poświadczenia, czy token dostępu współdzielonego signatures (SAS), czy certyfikat X.509 mogą być narażone na ataki. 

Rejestracja w usłudze Device Provisioning umożliwia urządzenia, aby był [automatycznie aprowizowane](concepts-auto-provisioning.md). Udostępnione urządzenie to taki, który został zarejestrowany z usługą IoT Hub, dzięki czemu umożliwia otrzymywanie jego początkowego [bliźniaczej reprezentacji urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stanu i rozpocząć raportowania danych telemetrycznych. W tym artykule opisano disenroll urządzenia z wystąpienia usługi aprowizacji, uniemożliwiając ponownie aprowizowane w przyszłości.

> [!NOTE] 
> Należy pamiętać o zasady ponawiania, urządzeń, które odwołać dostęp. Spróbuj na przykład urządzenie ma zasady ponawiania nieskończonej może stale zarejestrować za pomocą usługi aprowizacji. Taka sytuacja wykorzystuje zasoby usługi i prawdopodobnie ma wpływ na wydajność.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Lista zablokowanych urządzeń za pomocą wpisu rejestracji poszczególnych

Rejestracje indywidualne mają zastosowanie do jednego urządzenia i użyć certyfikatów X.509 lub tokenów SAS (w rzeczywistym lub wirtualnym module TPM) jako mechanizmu zaświadczania. (Urządzeń, które korzystają z tokenów sygnatur dostępu Współdzielonego zgodnie z ich mechanizmu zaświadczania mogą być udostępniane wyłącznie za pośrednictwem rejestracji indywidualnej.) Aby utworzyć listę niedozwolonych urządzeniu wyposażonym w rejestracji indywidualnej, możesz wyłączyć lub usunąć jego wpis rejestracji. 

Aby tymczasowo niedozwolonych urządzeń, wyłączając jej wpisu rejestracji: 

1. Zaloguj się w witrynie Azure portal i wybierz **wszystkie zasoby** menu po lewej stronie.
2. Na liście zasobów wybierz usługę aprowizacji, który chcesz utworzyć listę niedozwolonych urządzenia z.
3. Z Twoją usługą aprowizacji, wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **rejestracje indywidualne** kartę.
4. Wybierz wpis rejestracji dla urządzenia, które chcesz utworzyć listę niedozwolonych. 

    ![Wybierz swoje rejestracji indywidualnej](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na stronie rejestracji, przewiń w dół, a następnie wybierz pozycję **wyłączyć** dla **Włącz wpis** przełącznika, a następnie wybierz **Zapisz**.  

   ![Wyłącz wpis rejestracji indywidualnej w portalu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Aby trwale niedozwolonych urządzenia przez usunięcie jego wpis rejestracji:

1. Zaloguj się w witrynie Azure portal i wybierz **wszystkie zasoby** menu po lewej stronie.
2. Na liście zasobów wybierz usługę aprowizacji, który chcesz utworzyć listę niedozwolonych urządzenia z.
3. Z Twoją usługą aprowizacji, wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **rejestracje indywidualne** kartę.
4. Zaznacz pole wyboru obok wpisu rejestracji dla urządzenia, które chcesz utworzyć listę niedozwolonych. 
5. Wybierz **Usuń** w górnej części okna, a następnie wybierz pozycję **tak** aby upewnić się, że chcesz usunąć rejestrację. 

   ![Usuwanie wpisu rejestracji indywidualnej w portalu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Po zakończeniu procedury, powinien zostać wyświetlony wpis usunięty z listy rejestracje indywidualne.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Utworzyć listę niedozwolonych X.509 pośredni lub główny certyfikat urzędu certyfikacji za pomocą grupy rejestracji

Certyfikaty X.509 zwykle są rozmieszczone w łańcuch zaufania certyfikatów. Jeśli certyfikat na każdym etapie w łańcuchu zostanie naruszone bezpieczeństwo, relacja zaufania zostanie zerwana. Certyfikat musi na aby zapobiec usługi Device Provisioning aprowizacji urządzeń w podrzędnych w dowolnym łańcuchu zawierający certyfikat czarnej liście. Aby dowiedzieć się więcej na temat certyfikatów X.509 i jak są używane z usługą aprowizacji, zobacz [certyfikaty X.509](./concepts-security.md#x509-certificates). 

Wpis dla urządzeń, które można udostępniać wspólnego mechanizmu zaświadczania certyfikatów X.509 podpisany przez ten sam pośredni lub główny urząd certyfikacji jest w grupie rejestracji. Wpis grupy rejestracji jest skonfigurowany przy użyciu certyfikatu X.509, skojarzone z pośredniego lub głównego urzędu certyfikacji. Skonfigurowano wpis jest też żadnych konfiguracji wartości, takich jak stan bliźniaczej reprezentacji i połączenia Centrum IoT, które są udostępniane przez urządzenia przy użyciu tego certyfikatu w swoim łańcuchu certyfikatów. Aby utworzyć listę niedozwolonych certyfikatu, możesz wyłączyć lub usunąć jej grupy rejestracji.

Aby tymczasowo niedozwolonych certyfikat, wyłączając jej grupy rejestracji: 

1. Zaloguj się w witrynie Azure portal i wybierz **wszystkie zasoby** menu po lewej stronie.
2. Na liście zasobów wybierz usługę aprowizacji, który chcesz utworzyć listę niedozwolonych certyfikat podpisywania z.
3. Z Twoją usługą aprowizacji, wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **grup rejestracji** kartę.
4. Wybierz grupę rejestracji przy użyciu certyfikatu, który chcesz utworzyć listę niedozwolonych.
5. Wybierz **wyłączyć** na **Włącz wpis** przełącznika, a następnie wybierz **Zapisz**.  

   ![Wyłącz wpis grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Do trwale czarnej listy certyfikat, usuwając jego grupę rejestracji:

1. Zaloguj się w witrynie Azure portal i wybierz **wszystkie zasoby** menu po lewej stronie.
2. Na liście zasobów wybierz usługę aprowizacji, który chcesz utworzyć listę niedozwolonych urządzenia z.
3. Z Twoją usługą aprowizacji, wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **grup rejestracji** kartę.
4. Zaznacz pole wyboru obok grupy rejestracji dla certyfikatu, którego chcesz utworzyć listę niedozwolonych. 
5. Wybierz **Usuń** w górnej części okna, a następnie wybierz pozycję **tak** aby upewnić się, że chcesz usunąć grupę rejestracji. 

   ![Usuń wpis grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po zakończeniu procedury, powinien zostać wyświetlony wpis usunięty z listy grup rejestracji.  

> [!NOTE]
> Jeśli usuniesz grupę rejestracji dla certyfikatu, urządzeń, które mają certyfikat w swoim łańcuchu certyfikatów może nadal mieć możliwość rejestrowania, jeśli grupę rejestracji włączone dla certyfikatu głównego lub inny certyfikat pośredniego wyższego rzędu w certyfikacie istnieje łańcuch.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Lista zablokowanych urządzeń w grupie rejestracji

Urządzenia, które implementują mechanizm zaświadczania X.509 używają łańcucha certyfikatów urządzenia i klucz prywatny do uwierzytelniania. Gdy urządzenie łączy i uwierzytelnia się za pomocą usługi Device Provisioning Service, usługę szuka najpierw rejestrację indywidualną, który odpowiada poświadczenia urządzenia. Usługa następnie przeszukuje grup rejestracji w celu ustalenia, czy urządzenia mogą być udostępniane. Jeśli usługa znajdzie wyłączone rejestrację indywidualną dla urządzenia, uniemożliwia urządzeniu łączenie z. Usługa uniemożliwia połączenie, nawet jeśli istnieje grupa włączone rejestracji dla pośredniego lub głównego urzędu certyfikacji w łańcuchu certyfikatów urządzeń. 

Aby utworzyć listę niedozwolonych poszczególnych urządzeń, w grupie rejestracji, wykonaj następujące kroki:

1. Zaloguj się w witrynie Azure portal i wybierz **wszystkie zasoby** menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, który zawiera grupę rejestracji dla urządzenia, które chcesz utworzyć listę niedozwolonych.
3. Z Twoją usługą aprowizacji, wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **rejestracje indywidualne** kartę.
4. Wybierz **Dodaj rejestrację indywidualną** znajdujący się u góry. 
5. Na **Dodaj rejestracji** wybierz opcję **X.509** zaświadczania **mechanizm** dla tego urządzenia.

    Przekaż certyfikat urządzenia, a następnie wprowadź identyfikator urządzenia urządzenie jest na czarnej liście. Dla certyfikatu należy użyć certyfikatu podpisanego jednostki końcowej zainstalowane na urządzeniu. Urządzenie używa certyfikatu z podpisem jednostki końcowej dla uwierzytelniania.

    ![Ustaw właściwości urządzenia dla urządzenia zabronione](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Przewiń do dołu **Dodaj rejestracji** strony i wybierz **wyłączyć** na **Włącz wpis** przełącznika, a następnie wybierz **Zapisz**. 

    [![Używanie wyłączone wpisu rejestracji indywidualnej, aby wyłączyć urządzenie z grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Po pomyślnym utworzeniu rejestracji, rejestracja wyłączono urządzenie wymienione na powinien zostać wyświetlony **rejestracje indywidualne** kartę. 

## <a name="next-steps"></a>Kolejne kroki

Wyrejestrowywaniem jest również częścią większej proces anulowania aprowizacji. Anulowanie aprowizacji urządzenia obejmuje zarówno wyrejestrowywaniem z usługą aprowizacji i Wyrejestrowanie z usługi IoT hub. Aby dowiedzieć się więcej informacji na temat pełnego procesu, zobacz [jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego](how-to-unprovision-devices.md) 

