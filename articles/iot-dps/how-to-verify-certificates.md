---
title: Weryfikowanie certyfikatów urzędu certyfikacji X.509 za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak wykonać dowód posiadania certyfikatów urzędu certyfikacji X.509 za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973445"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak wykonać dowód posiadania certyfikatów X.509 urzędu certyfikacji w usłudze aprowizacji urządzeń

Zweryfikowany certyfikat urzędu certyfikacji X.509 (CA) to certyfikat urzędu certyfikacji, który został przekazany i zarejestrowany w usłudze inicjowania obsługi administracyjnej i przeszedł weryfikację posiadania usługi. 

Dowód posiadania obejmuje następujące kroki:
1. Uzyskaj unikatowy kod weryfikacyjny wygenerowany przez usługę inicjowania obsługi administracyjnej certyfikatu urzędu certyfikacji X.509. Możesz to zrobić w witrynie Azure Portal.
2. Utwórz certyfikat weryfikacji X.509 z kodem weryfikacyjnym jako jego podmiotem i podpisz certyfikat za pomocą klucza prywatnego skojarzonego z certyfikatem urzędu certyfikacji X.509.
3. Przekaż podpisany certyfikat weryfikacji do usługi. Usługa sprawdza poprawność certyfikatu weryfikacji przy użyciu publicznej części certyfikatu urzędu certyfikacji do zweryfikowannia, co dowodzi, że posiadasz klucz prywatny certyfikatu urzędu certyfikacji.

Zweryfikowane certyfikaty odgrywają ważną rolę podczas korzystania z grup rejestracji. Weryfikowanie własności certyfikatu zapewnia dodatkową warstwę zabezpieczeń, upewniając się, że przesyłający certyfikat jest w posiadaniu klucza prywatnego certyfikatu. Weryfikacja uniemożliwia złośliwemu aktorowi wąchanie ruchu przed wyodrębnieniem certyfikatu pośredniego i użyciem tego certyfikatu do utworzenia grupy rejestracji we własnej usłudze inicjowania obsługi administracyjnej, skutecznie przejmującej urządzenia. Udowadniając własność certyfikatu głównego lub pośredniego w łańcuchu certyfikatów, dowodzisz, że masz uprawnienia do generowania certyfikatów liścia dla urządzeń, które będą rejestrować się jako część tej grupy rejestracji. Z tego powodu certyfikat główny lub pośredni skonfigurowany w grupie rejestracji musi być zweryfikowanym certyfikatem lub musi zostać zwinięty do zweryfikowanego certyfikatu w łańcuchu certyfikatów, który urządzenie przedstawia podczas uwierzytelniania w usłudze. Aby dowiedzieć się więcej o grupach rejestracji, zobacz [Certyfikaty X.509](concepts-security.md#x509-certificates) i [Kontrolowanie dostępu urządzenia do usługi inicjowania obsługi administracyjnej za pomocą certyfikatów X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Zarejestruj publiczną część certyfikatu X.509 i uzyskaj kod weryfikacyjny

Aby zarejestrować certyfikat urzędu certyfikacji w usłudze inicjowania obsługi administracyjnej i uzyskać kod weryfikacyjny, którego można użyć podczas weryfikacji posiadania, wykonaj następujące kroki. 

1. W witrynie Azure portal przejdź do usługi inicjowania obsługi administracyjnej i otwórz **certyfikaty** z menu po lewej stronie. 
2. Kliknij **przycisk Dodaj,** aby dodać nowy certyfikat.
3. Wprowadź przyjazną nazwę wyświetlaną certyfikatu. Przejdź do pliku .cer lub pem reprezentującego publiczną część certyfikatu X.509. Kliknij pozycję **Przekaż**.
4. Po otrzymaniu powiadomienia o pomyślnym przesłaniu certyfikatu kliknij przycisk **Zapisz**.

    ![Przekazywanie certyfikatu](./media/how-to-verify-certificates/add-new-cert.png)  

   Certyfikat zostanie wyświetlona na liście **Eksplorator certyfikatów.** Należy zauważyć, że **stan** tego certyfikatu jest *niezweryfikowany*.

5. Kliknij certyfikat dodany w poprzednim kroku.

6. W **pliku Dane certyfikatu**kliknij pozycję **Generuj kod weryfikacyjny**.

7. Usługa inicjowania obsługi administracyjnej tworzy **kod weryfikacyjny,** który służy do sprawdzania poprawności własności certyfikatu. Skopiuj kod do schowka. 

   ![Weryfikowanie certyfikatu](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Cyfrowe podpisywanie kodu weryfikacyjnego w celu utworzenia certyfikatu weryfikacyjnego

Teraz musisz podpisać *kod weryfikacyjny* za pomocą klucza prywatnego skojarzonego z certyfikatem urzędu certyfikacji X.509, który generuje podpis. Jest to znane jako [dowód posiadania](https://tools.ietf.org/html/rfc5280#section-3.1) i powoduje podpisanie certyfikatu weryfikacyjnego.

Firma Microsoft udostępnia narzędzia i przykłady, które mogą pomóc w utworzeniu podpisanego certyfikatu weryfikacyjnego: 

- Zestaw **SDK języka C usługi Azure IoT Hub** zawiera skrypty programu PowerShell (Windows) i Bash (Linux), które ułatwiają tworzenie certyfikatów urzędu certyfikacji i liścia w celu tworzenia i wykonywania weryfikacji posiadania przy użyciu kodu weryfikacyjnego. Pliki [istotne](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) dla systemu można pobrać do folderu roboczego i postępować zgodnie z instrukcjami zawartymi w [readme Certyfikaty zarządzania urzędami certyfikacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) w celu przeprowadzenia weryfikacji posiadania certyfikatu urzędu certyfikacji. 
- Zestaw **SDK usługi Azure IoT Hub C#** zawiera [przykład weryfikacji certyfikatów grupowych,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)którego można użyć do wykonania weryfikacji posiadania.
 
> [!IMPORTANT]
> Oprócz wykonywania dowodu posiadania skrypty programu PowerShell i Bash cytowane wcześniej umożliwiają również tworzenie certyfikatów głównych, certyfikatów pośrednich i certyfikatów typu liść, które mogą być używane do uwierzytelniania i aprowizowania urządzeń. Certyfikaty te powinny być używane tylko do programowania. się do celów programisty. Nigdy nie powinny być używane w środowisku produkcyjnym. 

Skrypty programu PowerShell i Bash zawarte w dokumentacji i sdks opierają się na [OpenSSL](https://www.openssl.org/). Możesz również użyć OpenSSL lub innych narzędzi innych firm, aby pomóc Ci w dowodu posiadania. Aby uzyskać więcej informacji na temat narzędzi dostarczanych z zestawami SDK, zobacz [Jak korzystać z narzędzi zawartych w zestawach SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Przekazywanie podpisanego certyfikatu weryfikacyjnego

1. Przekaż podpis wynikowy jako certyfikat weryfikacji do usługi inicjowania obsługi administracyjnej w portalu. W **obszarze Szczegóły certyfikatu** w witrynie Azure portal użyj _ikony Eksploratora plików_ obok pola Certyfikat weryfikacji **.pem lub .cer,** aby przekazać podpisany certyfikat weryfikacji z systemu.

2. Po pomyślnym przesłaniu certyfikatu kliknij przycisk **Zweryfikuj**. Stan **STATUS** certyfikatu zmienia się na **_Zweryfikowany_** na liście **Eksplorator certyfikatów.** Kliknij **przycisk Odśwież,** jeśli nie zostanie zaktualizowany automatycznie.

   ![Prześlij weryfikację certyfikatu](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć grupę rejestracji za pomocą portalu, zobacz [Zarządzanie rejestracjami urządzeń za pomocą witryny Azure Portal](how-to-manage-enrollments.md).
- Aby dowiedzieć się, jak utworzyć grupę rejestracji za pomocą sdk usług, zobacz [Zarządzanie rejestracjami urządzeń za pomocą sdk usług](how-to-manage-enrollments-sdks.md).










