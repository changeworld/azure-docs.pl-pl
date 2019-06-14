---
title: Jak przeprowadzić dowodu posiadania dla certyfikatów X.509 urzędu certyfikacji za pomocą usługi Azure IoT Hub Device Provisioning | Dokumentacja firmy Microsoft
description: Sposób weryfikacji certyfikatów X.509 urzędu certyfikacji za pomocą usługi Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: afa4b3861e9fb7f91fd9f5d540353c5fad23efe0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "54913618"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak przeprowadzić dowodu posiadania dla certyfikatów X.509 urzędu certyfikacji za pomocą usługi Device Provisioning

Zweryfikowano X.509 urząd certyfikacji (CA) certyfikat jest certyfikat urzędu certyfikacji, który został przekazany i zarejestrowana w celu aprowizowania usługi i zrealizowano dowodu posiadania w usłudze. 

Dowód posiadania obejmuje następujące czynności:
1. Pobierz kod weryfikacyjny unikatowy generowane przez usługę aprowizacji dla certyfikatu X.509 urzędu certyfikacji. Można to zrobić w witrynie Azure portal.
2. Utwórz certyfikat X.509 weryfikacji z kodem weryfikacyjnym, jako jego podmiot i podpisanie certyfikatu przy użyciu klucza prywatnego skojarzonego za pomocą certyfikatu X.509 urzędu certyfikacji.
3. Przekaż certyfikat weryfikacji podpisanego z usługą. Usługa sprawdza poprawność certyfikatu weryfikacji za pomocą publiczną część certyfikatu urzędu certyfikacji, należy zweryfikować, zatem potwierdzające, że znajdują się w posiadaniu klucza prywatnego certyfikatu urzędu certyfikacji.

Zweryfikowanych certyfikatów odgrywa ważną rolę w przypadku korzystania z grup rejestracji. Weryfikowanie własności certyfikatu zawiera dodatkowa warstwa zabezpieczeń poprzez zapewnienie, że przekazujesz certyfikatu jest w posiadaniu klucza prywatnego certyfikatu. Weryfikacja zapobiega aktora złośliwego wykrywanie ruchu z wyodrębniania pośredniego certyfikatu oraz przy użyciu tego certyfikatu można utworzyć grupę rejestracji w ich własnych usługę aprowizacji, efektywnie przejęcie kontroli nad urządzeniami. Przez poświadczenie własność katalogu głównego lub pośredniego certyfikatu w łańcuchu certyfikatów, one potwierdzające, że masz uprawnienia do generowania certyfikatów liścia dla urządzeń, które spowoduje zarejestrowanie w ramach tej grupy rejestracji. Aby z tego powodu, certyfikat głównego lub pośredniego skonfigurowane w grupie rejestracji musi być zweryfikowany certyfikat, lub musi roll maksymalnie zweryfikowany certyfikat w łańcuchu certyfikatów urządzenia przedstawia podczas uwierzytelniania w usłudze. Aby dowiedzieć się więcej na temat grup rejestracji, zobacz [certyfikaty X.509](concepts-security.md#x509-certificates) i [sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Zarejestruj publiczną część certyfikatu X.509 i kodu weryfikacyjnego

Aby zarejestrować certyfikat urzędu certyfikacji z Twoją usługą aprowizacji i uzyskać kod weryfikacyjny, który można użyć podczas dowodu posiadania, wykonaj następujące kroki. 

1. W witrynie Azure portal przejdź do usługi aprowizacji, a następnie otwórz **certyfikaty** z menu po lewej stronie. 
2. Kliknij przycisk **Dodaj** można dodać nowego certyfikatu.
3. Wprowadź przyjazną nazwę wyświetlaną dla certyfikatu. Przejdź do pliku cer lub PEM, który reprezentuje publiczną część certyfikatu X.509. Kliknij pozycję **Przekaż**.
4. Gdy otrzymasz powiadomienie, że Twój certyfikat została pomyślnie przekazana, kliknij przycisk **Zapisz**.

    ![Przekazywanie certyfikatu](./media/how-to-verify-certificates/add-new-cert.png)  

   Twój certyfikat będzie wyświetlana w **Eksplorator certyfikatów** listy. Należy pamiętać, że **stan** tego certyfikatu jest *niezweryfikowane*.

5. Kliknij certyfikat, który zostało dodane w poprzednim kroku.

6. W **szczegóły certyfikatu**, kliknij przycisk **Generuj kod weryfikacyjny**.

7. Tworzy usługę aprowizowania **kod weryfikacyjny** służącego do weryfikowania własności certyfikatu. Skopiuj kod do Schowka. 

   ![Zweryfikuj certyfikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Podpisuj cyfrowo kod weryfikacyjny, aby utworzyć certyfikat weryfikacji

Teraz należy podpisać *kod weryfikacyjny* przy użyciu klucza prywatnego skojarzonego za pomocą certyfikatu X.509 urzędu certyfikacji, który generuje sygnaturę. Jest to nazywane [dowodu posiadania](https://tools.ietf.org/html/rfc5280#section-3.1) i wyników w weryfikacji podpisanego certyfikatu.

Firma Microsoft udostępnia narzędzia i przykłady, które mogą pomóc Ci utworzyć certyfikat z podpisem weryfikacji: 

- **Zestawu SDK usługi Azure IoT Hub C** zapewnia programu PowerShell (Windows) i skrypty powłoki Bash (Linux), ułatwiające tworzenie certyfikatów urzędu certyfikacji i liścia do tworzenia i wykonywanie dowodu posiadania za pomocą kodu weryfikacyjnego. Możesz pobrać [pliki](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) odpowiednie system do folderu roboczego i postępuj zgodnie z instrukcjami wyświetlanymi w [readme certyfikatów urzędu certyfikacji zarządzanie](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) przeprowadzić dowodu posiadania w certyfikacie urzędu certyfikacji. 
- **Azure IoT Hub zestawu SDK C#** zawiera [Group Certificate Verification Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), którego można użyć w celu dowodu posiadania.
 
> [!IMPORTANT]
> Oprócz wykonywania dowodu posiadania, skrypty programu PowerShell i programem Bash wymienionych wcześniej również pozwala na tworzenie certyfikatów głównych, certyfikaty pośrednie i certyfikatów liścia, używane do uwierzytelniania i aprowizować urządzenia. Te certyfikaty powinny być używane do tworzenia aplikacji tylko. One nie mogą być używane w środowisku produkcyjnym. 

Skrypty programu PowerShell i programem Bash, znajdujące się w dokumentacji i zestawów SDK polegają na [OpenSSL](https://www.openssl.org/). Można także użyć biblioteki OpenSSL lub innych narzędzi innych firm może ułatwić wykonywanie dowodu posiadania. Aby uzyskać więcej informacji na temat narzędzia dostarczane z zestawami SDK, zobacz [sposób korzystania z narzędzi dostępnych w zestawach SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Przekaż certyfikat weryfikacji podpisem

1. Przekaż wynikowy podpis jako certyfikat weryfikacji do usługi aprowizacji w portalu. W **szczegóły certyfikatu** w witrynie Azure portal, należy użyć _Eksploratora plików_ ikona obok pozycji **plik PEM lub cer certyfikatu weryfikacji** pola, aby przekazać podpisany certyfikat weryfikacji z systemu.

2. Po pomyślnym przekazaniu tego certyfikatu, kliknij przycisk **Sprawdź**. **Stan** zmian certyfikatu **_Verified_** w **Eksplorator certyfikatów** listy. Kliknij przycisk **Odśwież** Jeśli on nie być zaktualizowane automatycznie.

   ![Przekaż certyfikat weryfikacji](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o tym, jak korzystać z portalu, aby utworzyć grupę rejestracji, zobacz [Zarządzanie rejestracjami urządzeń przy użyciu witryny Azure portal](how-to-manage-enrollments.md).
- Aby dowiedzieć się więcej o tym, jak utworzyć grupę rejestracji za pomocą zestawów SDK usługi, zobacz [Zarządzanie rejestracjami urządzeń przy użyciu zestawów SDK usługi](how-to-manage-enrollments-sdks.md).










