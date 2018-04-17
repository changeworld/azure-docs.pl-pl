---
title: Jak to zrobić dowód z posiadania dla certyfikatów urzędów certyfikacji X.509 przy użyciu usługi inicjowania obsługi urządzeń Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Sposób weryfikacji certyfikatów X.509 urzędu certyfikacji z usługą punktu dystrybucji
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: dd54ca5a172bdb65b7781f2fb764b00f50e5df28
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak to zrobić dowód z posiadania dla certyfikatów urzędów certyfikacji X.509 przy użyciu usługi inicjowania obsługi urządzeń

Zweryfikowano X.509 certyfikatu urzędu certyfikacji certyfikat jest certyfikatem urzędu certyfikacji, przekazane i zarejestrowany z zainicjowanie obsługi usługi i zrealizowano dowód z posiadania z usługą. 

"Dowodu posiadania" obejmuje następujące kroki:
1. Pobierz weryfikacji unikatowy kod wygenerowany przez usługę inicjowania obsługi administracyjnej dla certyfikatu X.509 urzędu certyfikacji. Można to zrobić w portalu Azure.
2. Utwórz certyfikat X.509 weryfikacji z kod weryfikacyjny, jako jego podmiotu i podpisania certyfikatu z kluczem prywatnym skojarzone z certyfikat X.509 urzędu certyfikacji.
3. Przekaż certyfikat weryfikacji podpisanej do usługi. Usługa sprawdza poprawność certyfikatu weryfikacji przy użyciu część publiczną certyfikatu urzędu certyfikacji należy zweryfikować, w związku z tym potwierdzające, że znajdują się w posiadaniu klucza prywatnego certyfikatu urzędu certyfikacji.

Zweryfikowano certyfikaty odgrywa ważną rolę, podczas korzystania z grup rejestracji. Weryfikowania własności certyfikatu zapewnia warstwę dodatkowe zabezpieczenia przez zapewnienie im przekazujący certyfikat będącą w posiadaniu klucza prywatnego certyfikatu. Weryfikacja zapobiega aktora złośliwego wykrywanie ruchu z wyodrębnianie certyfikatu pośredniego i przy użyciu tego certyfikatu do utworzenia grupy rejestracji w ich własnych inicjowania obsługi usługi skutecznie przejęcie kontroli nad urządzeniami. Przez poświadczenie prawo własności do katalogu głównego lub pośredniego certyfikatu w łańcuchu certyfikatów, jest potwierdzające, że masz uprawnienia do wygenerowania liścia certyfikatów dla urządzeń, które spowoduje zarejestrowanie w ramach tej grupy rejestracji. Z tego powodu, katalog główny lub pośredni certyfikatu skonfigurowanego w grupie rejestracji musi zostać zweryfikowany certyfikatu lub musi zbiorczego maksymalnie zweryfikowano certyfikat w łańcuchu certyfikatów urządzenia przedstawia podczas uwierzytelniania w usłudze. Aby dowiedzieć się więcej o grupach rejestracji, zobacz [certyfikatów X.509](concepts-security.md#x509-certificates) i [kontrolowanie dostępu do urządzenia do inicjowania obsługi usługi za pomocą certyfikatów X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Zarejestruj element publicznego certyfikatu X.509 i kod weryfikacyjny

Aby zarejestrować certyfikat urzędu certyfikacji przy użyciu usługi inicjowania obsługi administracyjnej i uzyskać kod weryfikacyjny, który możesz użyć podczas "dowodu posiadania", wykonaj następujące kroki. 

1. W portalu Azure, przejdź do inicjowania obsługi usługi i Otwórz **certyfikaty** z menu po lewej stronie. 
2. Kliknij przycisk **Dodaj** można dodać nowego certyfikatu.
3. Wprowadź przyjazną nazwę wyświetlaną dla certyfikatu. Przejdź do pliku cer lub PEM, przedstawiającą publicznego certyfikatu X.509. Kliknij pozycję **Przekaż**.
4. Gdy otrzymasz powiadomienie o pomyślnym przekazaniu certyfikat, kliknij przycisk **zapisać**.

    ![Przekazywanie certyfikatu](./media/how-to-verify-certificates/add-new-cert.png)  

   Certyfikat będzie widoczny w **Explorer certyfikatu** listy. Należy pamiętać, że **stan** tego certyfikatu jest *Unverified*.

5. Kliknij certyfikat, który dodanym w poprzednim kroku.

6. W **szczegóły certyfikatu**, kliknij przycisk **Generuj kod weryfikacyjny**.

7. Tworzy usługę inicjowania obsługi administracyjnej **kod weryfikacyjny** czy służy do sprawdzenia poprawności prawa własności certyfikatu. Skopiuj kod do Schowka. 

   ![Zweryfikować certyfikatu](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Podpisuj cyfrowo kod weryfikacyjny, aby utworzyć certyfikat weryfikacji

Teraz, musisz zarejestrować *kod weryfikacyjny* przy użyciu klucza prywatnego skojarzonego z certyfikat X.509 urzędu certyfikacji, który generuje sygnaturę. Jest to nazywane [dowodu posiadania](https://tools.ietf.org/html/rfc5280#section-3.1) i wyniki weryfikacji podpisanego certyfikatu.

Firma Microsoft udostępnia narzędzia i przykłady, które mogą pomóc Ci utworzyć certyfikat z podpisem weryfikacji: 

- **Zestawu SDK usługi Azure IoT Hub C** zapewnia środowiska PowerShell (Windows) i skrypty powłoki systemowej (Linux), ułatwiające tworzenie urzędu certyfikacji i liścia certyfikatów do tworzenia aplikacji i przeprowadzanie przy użyciu kodu weryfikacyjnego dowód z posiadania. Możesz pobrać [pliki](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) istotne dla systemu do folderu roboczego i postępuj zgodnie z instrukcjami w [readme certyfikatów urzędu certyfikacji zarządzanie](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) do wykonania "dowodu posiadania" w certyfikacie urzędu certyfikacji. 
- **Azure IoT Hub C# SDK** zawiera [grupy certyfikatu weryfikacji próbki](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), którego można użyć w celu "dowodu posiadania".
- Możesz wykonać kroki opisane w [skryptów programu PowerShell do zarządzania certyfikatami X.509 podpisany przez urząd certyfikacji](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates) artykułu w dokumentacji Centrum IoT, w szczególności skrypt wymienionych w sekcji [dowodu posiadania programu Certyfikat X.509 urzędu certyfikacji](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode).
 
> [!IMPORTANT]
> Oprócz przeprowadzania dowód z posiadania, skrypty programu PowerShell i Bash wcześniej odnosiło się również pozwalają tworzyć certyfikaty główne, certyfikaty pośrednie i certyfikaty typu liść, które mogą służyć do uwierzytelniania i udostępnianie urządzeń. Te certyfikaty powinny być używane dla rozwoju tylko. Ich nie mogą być używane w środowisku produkcyjnym. 

Skrypty programu PowerShell i Bash, znajdujące się w dokumentacji i zestawy SDK polegać na [OpenSSL](https://www.openssl.org/). Pomaga "dowodu posiadania", może używać biblioteki OpenSSL lub innych narzędzi innych firm. Aby uzyskać więcej informacji dotyczących narzędzia wyposażone w zestawy SDK, zobacz [sposobu korzystania z narzędzi dostępnych w zestawy SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Przekaż certyfikat weryfikacji podpisem

1. Przekaż wynikowy podpisu jako certyfikat weryfikacji do inicjowania obsługi usługi w portalu. W **szczegóły certyfikatu** w portalu Azure za pomocą _Eksploratora plików_ obok opcji **plik PEM lub .cer certyfikatu weryfikacji** pola, aby przekazać podpisany certyfikat weryfikacji z systemu.

2. Po pomyślnym przekazaniu certyfikat, kliknij przycisk **Sprawdź**. **Stan** zmian certyfikatu **_zweryfikowano_** w **Explorer certyfikatu** listy. Kliknij przycisk **Odśwież** nie automatycznie aktualizacji.

   ![Przekaż certyfikat weryfikacji](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o tym, jak korzystać z portalu do utworzenia grupy rejestracji, zobacz [Zarządzanie rejestracji urządzenia z portalu Azure](how-to-manage-enrollments.md).
- Aby dowiedzieć się więcej o tym, jak korzystać z zestawów SDK usługi do utworzenia grupy rejestracji, zobacz [Zarządzanie rejestracji urządzeń z usługi SDK](how-to-manage-enrollments-sdks.md).










