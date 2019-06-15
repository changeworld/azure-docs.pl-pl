---
title: Usługi platformy Azure IoT Hub urządzenia aprowizacji - zaświadczenie klucza symetrycznego
description: Ten artykuł zawiera omówienie pojęć dotyczących symetrycznego zaświadczenie klucza przy użyciu IoT Device Provisioning.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746184"
---
# <a name="symmetric-key-attestation"></a>Zaświadczanie klucza symetrycznego

W tym artykule opisano proces zaświadczania tożsamości, gdy z usługą Device Provisioning przy użyciu kluczy symetrycznych. 

Zaświadczenie klucza symetrycznego jest proste podejście do uwierzytelniania urządzeń przy użyciu wystąpienia usługi Device Provisioning. Ta metoda zaświadczania reprezentuje środowisko "Hello world" dla deweloperów, którzy dopiero Inicjowanie obsługi administracyjnej urządzeń lub nie ma wymagań dotyczących zabezpieczeń z ograniczeniami. Urządzenie korzysta z zaświadczania [modułu TPM](concepts-tpm-attestation.md) lub [certyfikat X.509](concepts-security.md#x509-certificates) jest więcej bezpieczny i powinna być używana w bardziej rygorystycznych wymagań dotyczących zabezpieczeń.

Rejestracje klucza symetrycznego także udostępnić to doskonały sposób starsze urządzenia, dzięki funkcji ograniczonych zabezpieczeń do uruchamiania w chmurze za pomocą usługi Azure IoT. Aby uzyskać więcej informacji na temat symetrycznego zaświadczenie klucza przy użyciu starszych urządzeń, zobacz [sposobu użycia kluczy symetrycznych przy użyciu starszych urządzeń](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Tworzenie klucza symetrycznego

Domyślnie usługi Device Provisioning Service tworzy nowe klucze symetryczne o długości domyślnego 32-bitowej po zapisaniu na nowe rejestracje z **automatycznie Generuj klucze** po włączeniu opcji.

![Klucze symetryczne Generuj automatycznie](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Po wyłączeniu tej opcji, możesz także podać własne klucze symetryczne do rejestracji. Podczas określania klucze symetryczne, klucze musi mieć klucz o długości od 16 bajtów do 64 bajtów. Ponadto klucze symetryczne musi być podana w prawidłowym formacie Base64.



## <a name="detailed-attestation-process"></a>Proces zaświadczania szczegółowe

Zaświadczenie klucza symetrycznego, przy użyciu usługi Device Provisioning jest wykonywane, korzystając z tych samych [tokenów zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-token-structure) obsługiwane przez centra IoT Hub, aby zidentyfikować urządzenia. Te tokeny zabezpieczające są [tokeny sygnatury dostępu współdzielonego (SAS)](../service-bus-messaging/service-bus-sas.md). 

Tokeny sygnatur dostępu Współdzielonego mają skrótu *podpisu* utworzonego przy użyciu klucza symetrycznego. Podpis jest ponownie tworzone przez usługę aprowizacji urządzeń, aby sprawdzić, czy token zabezpieczający prezentowane podczas zaświadczania jest autentyczna, czy nie.

Tokeny sygnatur dostępu Współdzielonego mają następującą postać:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono składniki każdy token:

| Wartość | Opis |
| --- | --- |
| {Sygnatura} |Ciąg sygnatury HMAC SHA256. Dla rejestracji indywidualnych ten podpis jest generowany przy użyciu klucza symetrycznego (podstawowe lub pomocnicze), aby wykonać skrót. W przypadku grup rejestracji klucza generowanego z klucza rejestracji grupy służy do wykonywania skrót. Wartość skrótu jest wykonywana na komunikat w postaci: `URL-encoded-resourceURI + "\n" + expiry`. **Ważne**: Klucz musi zostać odczytany z formatu base64 przed używany do wykonywania obliczeń HMAC SHA256. Ponadto wynik podpisu musi być zakodowane w adresie URL. |
| {resourceURI} |Identyfikator URI punktu końcowego rejestracji, który jest możliwy z tym tokenem, począwszy od Identyfikatora zakresu dla tego wystąpienia usługi Device Provisioning. Na przykład: `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |Ciągi UTF8 liczba sekund od epoki 00:00:00 czasu UTC na 1 stycznia 1970. |
| {URL-encoded-resourceURI} |Małe zamierzone, Zapisz kodowania adresu URL identyfikator URI zasobu małymi literami |
| {policyName} |Nazwa zasad dostępu współdzielonego, do którego odwołuje się ten token. Nazwa zasad używanych podczas aprowizowania za pomocą zaświadczenie klucza symetrycznego jest **rejestracji**. |

Gdy urządzenie jest zaświadczanie o rejestrację indywidualną, urządzenie używa klucza symetrycznego zdefiniowane w wpisu rejestracji indywidualnej do tworzenia skrótu podpisu tokenu sygnatury dostępu Współdzielonego.

Aby uzyskać przykłady kodu, które utworzyć token sygnatury dostępu Współdzielonego, zobacz [tokenów zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Tworzenie tokenów zabezpieczających, aby uzyskać zaświadczenie klucza symetrycznego jest obsługiwane przez zestaw SDK C usługi Azure IoT. Na przykład zaświadczania za pomocą rejestracji indywidualnej przy użyciu zestawu SDK C usługi IoT platformy Azure, zobacz [Aprowizowanie symulowanego urządzenia przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Grupy rejestracji

Klucze symetryczne dla rejestracji grup nie są używane bezpośrednio przez urządzenia, podczas inicjowania obsługi administracyjnej. Zamiast tego urządzenia, które należą do rejestrowania grupy aprowizacji przy użyciu klucza pochodnego urządzenia. 

Po pierwsze identyfikator rejestracji jest zdefiniowana dla każdego urządzenia zaświadczanie o grupie rejestracji. Prawidłowe znaki dla Identyfikatora rejestracji to małe znaki alfanumeryczne i kreski ("-"). Ten identyfikator rejestracji powinien być unikatowym identyfikujących urządzenie. Na przykład starszego urządzenia mogą nie obsługiwać wielu funkcji zabezpieczeń. Starsze urządzenia mogą mieć adres MAC lub numer seryjny jest dostępny do unikatowej identyfikacji tego urządzenia. W takiej sytuacji identyfikator rejestracji może składać się z adresu MAC i numer seryjny jest podobny do następującego:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

W tym przykładzie dokładnie jest używany w [jak inicjować obsługę starszych urządzeń przy użyciu kluczy symetrycznych](how-to-legacy-device-symm-key.md) artykułu.

Gdy identyfikator rejestracji została zdefiniowana dla tego urządzenia, klucz symetryczny dla grupy rejestracji jest używany do obliczenia [HMAC SHA256](https://wikipedia.org/wiki/HMAC) skrótu identyfikator rejestracji w celu wygenerowania klucza pochodnego urządzenia. Mieszanie identyfikator rejestracji można wykonać przy użyciu następującego kodu C#:

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Wynikowy klucz urządzenia jest następnie używany do generowania tokenu sygnatury dostępu Współdzielonego ma być używany na potrzeby zaświadczania. Każde urządzenie w grupie rejestracji jest wymagana do poświadczenia, za pomocą tokenu zabezpieczeń wygenerowanych z Unikatowy klucza pochodnego. Klucz symetryczny grupy rejestracji nie można używać bezpośrednio na potrzeby zaświadczania.

#### <a name="installation-of-the-derived-device-key"></a>Instalacja klucza pochodnego urządzenia

Najlepiej klucze urządzeń są uzyskane i zainstalowane w fabryce. Ta metoda gwarancję, że klucz grupy nigdy nie jest uwzględniony w oprogramowanie wdrożone na urządzeniu. Gdy urządzenie jest przypisany adres MAC lub numer seryjny, klucz może pochodnej i wprowadzony na urządzeniu, natomiast producenta zdecyduje się na przechowywanie ich.

Należy wziąć pod uwagę Poniższy diagram, który przedstawia tabelę klucze urządzeń wygenerowane w fabryce mieszania identyfikator rejestracji każdego urządzenia przy użyciu klucza rejestracji grupy (**K**). 

![Klucze urządzeń, które przypisane z fabryki](./media/concepts-symmetric-key-attestation/key-diversification.png)

Tożsamość każdego urządzenia jest reprezentowany przez identyfikator rejestracji i klucz urządzenia pochodnej, który jest zainstalowany w fabryce. Klucz urządzenia nigdy nie jest kopiowany do innej lokalizacji, a klucz grupy nigdy nie jest przechowywany na urządzeniu.

Jeśli klucze urządzeń nie są zainstalowane w tej fabryce [przez moduł HSM do sprzętowego modułu zabezpieczeń](concepts-security.md#hardware-security-module) stosuje się do bezpiecznego przechowywania tożsamości urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz zrozumienia zaświadczenie klucza symetrycznego, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Szybki start: Aprowizowanie symulowanego urządzenia przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)
* [Dowiedz się więcej o kwestie poruszane w automatycznej aprowizacji](./concepts-auto-provisioning.md)
* [Rozpoczęcie korzystania z automatycznej aprowizacji](./quick-setup-auto-provision.md) 
