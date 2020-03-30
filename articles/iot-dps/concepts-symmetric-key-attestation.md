---
title: Usługa inicjowania obsługi administracyjnej urządzeń usługi Usługi Azure IoT Hub — zaświadczanie klucza symetrycznego
description: Ten artykuł zawiera omówienie koncepcyjne atestu klucza symetrycznego przy użyciu usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271514"
---
# <a name="symmetric-key-attestation"></a>Zaświadczanie klucza symetrycznego

W tym artykule opisano proces zaświadczania tożsamości podczas korzystania z kluczy symetrycznych z usługą inicjowania obsługi administracyjnej urządzeń. 

Zaświadczanie klucza symetrycznego jest proste podejście do uwierzytelniania urządzenia z wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Ta metoda zaświadczania reprezentuje środowisko "Hello world" dla deweloperów, którzy są nowicjuszami w inicjowaniu obsługi administracyjnej urządzeń lub nie mają ścisłych wymagań dotyczących zabezpieczeń. Zaświadczanie urządzenia przy użyciu modułu [TPM](concepts-tpm-attestation.md) lub [certyfikatu X.509](concepts-security.md#x509-certificates) jest bezpieczniejsze i powinno być używane do bardziej rygorystycznych wymagań dotyczących zabezpieczeń.

Symetryczne rejestracje kluczy zapewniają również doskonały sposób dla starszych urządzeń, z ograniczoną funkcjonalnością zabezpieczeń, do uruchamiania wersji do chmury za pośrednictwem usługi Azure IoT. Aby uzyskać więcej informacji na temat zaświadczania klucza symetrycznego ze starszymi urządzeniami, zobacz [Jak używać kluczy symetrycznych ze starszymi urządzeniami.](how-to-legacy-device-symm-key.md)


## <a name="symmetric-key-creation"></a>Tworzenie kluczy symetrycznych

Domyślnie usługa inicjowania obsługi administracyjnej urządzeń tworzy nowe klucze symetryczne o domyślnej długości 32 bajtów, gdy nowe rejestracje są zapisywane z włączoną opcją **Automatyczne generowanie kluczy.**

![Automatyczne generowanie kluczy symetrycznych](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Można również podać własne klucze symetryczne dla rejestracji, wyłączając tę opcję. Podczas określania własnych kluczy symetrycznych, klucze muszą mieć długość klucza między 16 bajtów i 64 bajtów. Ponadto klucze symetryczne muszą być dostarczane w prawidłowym formacie Base64.



## <a name="detailed-attestation-process"></a>Szczegółowy proces zaświadczania

Zaświadczanie klucza symetrycznego za pomocą usługi inicjowania obsługi administracyjnej urządzeń jest wykonywane przy użyciu tych [samych tokenów zabezpieczających obsługiwanych](../iot-hub/iot-hub-devguide-security.md#security-token-structure) przez centra IoT w celu identyfikacji urządzeń. Te tokeny zabezpieczające są [tokenami sygnatury dostępu współdzielonego (SAS).](../service-bus-messaging/service-bus-sas.md) 

Tokeny sygnatury dostępu Współdzielonego mają *sygnaturę* mieszaną, która jest tworzona przy użyciu klucza symetrycznego. Podpis jest odtworzony przez usługę inicjowania obsługi administracyjnej urządzeń w celu sprawdzenia, czy token zabezpieczający przedstawiony podczas zaświadczania jest autentyczny, czy nie.

Tokeny sygnatury dostępu Współdzielonego mają następujący formularz:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto składniki każdego tokenu:

| Wartość | Opis |
| --- | --- |
| {podpis} |Ciąg podpisu HMAC-SHA256. W przypadku rejestracji indywidualnych ten podpis jest produkowany przy użyciu klucza symetrycznego (podstawowego lub pomocniczego) do wykonania skrótu. W przypadku grup rejestracji klucz pochodzący z klucza grupy rejestracji jest używany do wykonywania skrótu. Skrót jest wykonywany na wiadomości formularza: `URL-encoded-resourceURI + "\n" + expiry`. **Ważne:** Klucz musi zostać zdekodowany z base64 przed użyciem do wykonywania obliczeń HMAC-SHA256. Ponadto wynik podpisu musi być zakodowany w adresie URL. |
| {resourceURI} |Identyfikator URI punktu końcowego rejestracji, do który można uzyskać dostęp za pomocą tego tokenu, począwszy od identyfikatora zakresu dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń. Na przykład: `{Scope ID}/registrations/{Registration ID}` |
| {wygaśnięcie} |Ciągi UTF8 przez kilka sekund od 00:00:00 UTC w dniu 1 stycznia 1970. |
| {IdentyfikatorURI zakodowany w adresie URL} |Małe wielkości kodowania adresów URL mniejszego identyfikatora URI zasobu wielkości liter |
| {nazwa_polityki} |Nazwa zasady dostępu współdzielonego, do której odnosi się ten token. Nazwa zasad używana podczas inicjowania obsługi administracyjnej z zaświadczaniem klucza symetrycznego jest **rejestracja**. |

Gdy urządzenie jest testowane z rejestracją indywidualną, urządzenie używa klucza symetrycznego zdefiniowanego we wpisie rejestracji indywidualnej do utworzenia sygnatury skrótowej dla tokenu sygnatury dostępu Współdzielonego.

Aby zapoznać się z przykładami kodu, które tworzą token sygnatury dostępu [Współdzielonego, zobacz Tokeny zabezpieczające](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Tworzenie tokenów zabezpieczających dla zaświadczania klucza symetrycznego jest obsługiwane przez zestaw SDK C usługi Azure IoT. Na przykład przy użyciu sdk C usługi Azure IoT do testowania przy użyciu rejestracji indywidualnej, zobacz [Aprowizuj symulowane urządzenie z kluczami symetrycznymi.](quick-create-simulated-device-symm-key.md)


## <a name="group-enrollments"></a>Rejestracje grupowe

Klucze symetryczne dla rejestracji grup nie są używane bezpośrednio przez urządzenia podczas inicjowania obsługi administracyjnej. Zamiast tego urządzenia, które należą do aprowizowania grupy rejestracji przy użyciu pochodnego klucza urządzenia. 

Najpierw unikatowy identyfikator rejestracji jest zdefiniowany dla każdego urządzenia posadzącego grupę rejestracji. Prawidłowe znaki dla identyfikatora rejestracji to małe litery alfanumeryczne i myślnik ('-'). Ten identyfikator rejestracji powinien być unikatowy, który identyfikuje urządzenie. Na przykład starsze urządzenie może nie obsługiwać wielu funkcji zabezpieczeń. Starsze urządzenie może mieć tylko adres MAC lub numer seryjny dostępny do jednoznacznej identyfikacji tego urządzenia. W takim przypadku identyfikator rejestracji może składać się z adresu MAC i numeru seryjnego podobnego do następującego:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Ten dokładny przykład jest używany w [Jak aprowizować starsze urządzenia przy użyciu kluczy symetrycznych](how-to-legacy-device-symm-key.md) artykułu.

Po zdefiniowaniu identyfikatora rejestracji dla urządzenia klucz symetryczny dla grupy rejestracji jest używany do obliczania skrótu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji w celu uzyskania pochodnego klucza urządzenia. Mieszanie identyfikatora rejestracji można wykonać za pomocą następującego kodu Języka C#:

```csharp
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

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Wynikowy klucz urządzenia jest następnie używany do generowania tokenu sygnatury dostępu Współdzielonego, który ma być używany do zaświadczania. Każde urządzenie w grupie rejestracji jest wymagane do testowania przy użyciu tokenu zabezpieczającego wygenerowanego na podstawie unikatowego klucza pochodnego. Klucz symetryczny grupy rejestracji nie może być używany bezpośrednio do zaświadczania.

#### <a name="installation-of-the-derived-device-key"></a>Instalacja klucza urządzenia pochodnego

Najlepiej, jeśli klucze urządzenia są wyprowadzane i instalowane fabrycznie. Ta metoda gwarantuje, że klucz grupy nigdy nie jest zawarty w żadnym oprogramowaniu wdrożonym na urządzeniu. Gdy urządzeniu jest przypisywany adres MAC lub numer seryjny, klucz może być uzyskany i wstrzyknięty do urządzenia, niezależnie od tego, czy producent zdecyduje się go zapisać.

Rozważmy poniższy diagram, który przedstawia tabelę kluczy urządzenia wygenerowanych w fabryce przez mieszanie każdego identyfikatora rejestracji urządzenia za pomocą klucza rejestracji grupy (**K**). 

![Klucze urządzenia przypisane z fabryki](./media/concepts-symmetric-key-attestation/key-diversification.png)

Tożsamość każdego urządzenia jest reprezentowana przez identyfikator rejestracji i pochodny klucz urządzenia, który jest zainstalowany fabrycznie. Klucz urządzenia nigdy nie jest kopiowany do innej lokalizacji, a klucz grupy nigdy nie jest przechowywany na urządzeniu.

Jeśli klucze urządzenia nie są zainstalowane fabrycznie, [sprzętowy moduł zabezpieczeń HSM](concepts-security.md#hardware-security-module) powinien być używany do bezpiecznego przechowywania tożsamości urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wiedzę na temat zaświadczania klucza symetrycznego, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)
* [Dowiedz się więcej o pojęciach dotyczących automatycznego inicjowania obsługi administracyjnej](./concepts-auto-provisioning.md)
* [Wprowadzenie do korzystania z automatycznego inicjowania obsługi administracyjnej](./quick-setup-auto-provision.md) 
