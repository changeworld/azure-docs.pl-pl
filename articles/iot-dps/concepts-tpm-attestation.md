---
title: Usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub — zaświadczanie tpm
description: Ten artykuł zawiera omówienie koncepcyjne przepływu zaświadczania modułu TPM przy użyciu usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975282"
---
# <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

Usługa inicjowania obsługi administracyjnej urządzeń w centrum IoT hub jest usługą pomocniczą dla usługi IoT Hub, której używasz do konfigurowania obsługi administracyjnej urządzeń bezdotykowych do określonego centrum IoT Hub. Dzięki usłudze aprowizacji urządzeń można aprowizować miliony urządzeń w bezpieczny sposób.

W tym artykule opisano proces zaświadczania tożsamości podczas korzystania z modułu [TPM](./concepts-device.md). Moduł TPM oznacza moduł zaufanej platformy i jest typem sprzętowego modułu zabezpieczeń (HSM). W tym artykule założono, że używasz dyskretnego, firmware lub zintegrowanego modułu TPM. Emulowane programowo moduły TPM doskonale nadają się do prototypowania lub testowania, ale nie zapewniają takiego samego poziomu bezpieczeństwa, jak dyskretne, firmware lub zintegrowane moduły TPM. Nie zaleca się używania programowych programów TPM w produkcji. Aby uzyskać więcej informacji na temat typów modułów TPM, zobacz [Krótkie wprowadzenie do modułu TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Ten artykuł dotyczy tylko urządzeń korzystających z modułu TPM 2.0 z obsługą kluczy HMAC i ich kluczami uzupełniającymi. Nie jest przeznaczony dla urządzeń używających certyfikatów X.509 do uwierzytelniania. Moduł TPM jest ogólnobranżowym standardem ISO od Trusted Computing Group, a więcej o tpm można przeczytać w [pełnej specyfikacji modułu TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) lub [specyfikacji ISO/IEC 11889.](https://www.iso.org/standard/66510.html) W tym artykule przyjęto również założenie, że znasz pary kluczy publicznych i prywatnych oraz sposób ich użycia do szyfrowania.

SDK urządzeń inicjowania obsługi urządzeń aprowizujących urządzenia obsługują wszystko, co jest opisane w tym artykule dla Ciebie. Nie ma potrzeby implementowania niczego dodatkowego, jeśli używasz sdków na swoich urządzeniach. Ten artykuł pomaga zrozumieć koncepcyjnie, co dzieje się z układem zabezpieczeń modułu TPM, gdy urządzenie jest wymagane i dlaczego jest tak bezpieczne.

## <a name="overview"></a>Omówienie

TPM używać czegoś, co nazywa klucz poręczenia (EK) jako bezpieczny katalog główny zaufania. EK jest unikalny dla modułu TPM i jego zmiana zasadniczo zmienia urządzenie na nowe.

Istnieje inny typ klucza, który mają TPM, zwany kluczem głównym magazynu (SRK). SRK może być generowany przez właściciela modułu TPM po przejmuje na własność modułu TPM. Przejęcie na własność modułu TPM jest specyficzny dla modułu TPM, mówiąc "ktoś ustawia hasło na modułie HSM". Jeśli urządzenie TPM jest sprzedawane nowemu właścicielowi, nowy właściciel może przejąć na własność moduł TPM, aby wygenerować nowy SRK. Nowa generacja SRK zapewnia, że poprzedni właściciel nie może używać modułu TPM. Ponieważ SRK jest unikatowy dla właściciela modułu TPM, SRK może służyć do uszczelniania danych do modułu TPM dla tego właściciela. SRK zapewnia piaskownicy dla właściciela do przechowywania kluczy i zapewnia możliwość odwołania dostępu, jeśli urządzenie lub moduł TPM jest sprzedawany. To jak przeprowadzka do nowego domu: przejęcie własności zmienia zamki na drzwiach i niszczy wszystkie meble pozostawione przez poprzednich właścicieli (SRK), ale nie można zmienić adresu domu (EK).

Po skonfigurowaniu i przygotowaniu urządzenia do użycia będzie ono dostępne zarówno EK, jak i SRK.

![Przejęcie na własność modułu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Jedna uwaga dotycząca przejęcia na własność modułu TPM: Przejęcie na własność modułu TPM zależy od wielu rzeczy, w tym od producenta modułu TPM, zestawu używanych narzędzi modułu TPM i systemu operacyjnego urządzenia. Postępuj zgodnie z instrukcjami odpowiednimi dla systemu, aby przejąć na własność.

Usługa inicjowania obsługi administracyjnej urządzeń używa publicznej części EK (EK_pub) do identyfikowania i rejestrowania urządzeń. Dostawca urządzenia może odczytać EK_pub podczas produkcji lub ostatecznego testowania i przekazać EK_pub do usługi inicjowania obsługi administracyjnej, dzięki czemu urządzenie zostanie rozpoznane po nawiązaniu połączenia z aprowizyją. Usługa inicjowania obsługi administracyjnej urządzeń nie sprawdza srk lub właściciela, więc "wyczyszczenie" modułu TPM usuwa dane klienta, ale EK (i inne dane dostawcy) jest zachowywany i urządzenie będzie nadal rozpoznawane przez usługę inicjowania obsługi urządzeń, gdy łączy się z aprowizją.

## <a name="detailed-attestation-process"></a>Szczegółowy proces zaświadczania

Gdy urządzenie z modułem TPM po raz pierwszy łączy się z usługą inicjowania obsługi administracyjnej urządzeń, usługa najpierw sprawdza podane EK_pub względem EK_pub przechowywane na liście rejestracji. Jeśli EK_pubs nie są zgodne, urządzenie nie może być aprowizować. Jeśli EK_pubs są zgodne, usługa wymaga, aby urządzenie udowodniło własność prywatnej części EK poprzez wyzwanie typu nonce, które jest bezpiecznym wyzwaniem używanym do udowodnienia tożsamości. Usługa inicjowania obsługi administracyjnej urządzeń generuje urządzenie typu nonce, a następnie szyfruje go za pomocą SRK, a następnie EK_pub, które są dostarczane przez urządzenie podczas początkowego wywołania rejestracji. Moduł TPM zawsze zapewnia bezpieczeństwo prywatnej części EK. Zapobiega to podrabianiu i zapewnia, że tokeny SAS są bezpiecznie aprowizacji do autoryzowanych urządzeń.

Przejdźmy szczegółowo przez proces zaświadczania.

### <a name="device-requests-an-iot-hub-assignment"></a>Urządzenie żąda przypisania usługi IoT Hub

Najpierw urządzenie łączy się z usługą inicjowania obsługi administracyjnej urządzeń i żąda ich udostępnienia. W ten sposób urządzenie udostępnia usługę z identyfikatorem rejestracji, zakresem identyfikatora oraz EK_pub i SRK_pub z modułu TPM. Usługa przekazuje zaszyfrowane nonce z powrotem do urządzenia i prosi urządzenie, aby odszyfrować nonce i użyć go do podpisania tokenu Sygnatury dostępu Współdzielonego, aby połączyć się ponownie i zakończyć inicjowanie obsługi administracyjnej.

![Inicjowanie obsługi administracyjnej żądań urządzeń](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Wyzwanie nonce

Urządzenie pobiera nonce i używa prywatnych części EK i SRK do odszyfrowania nonce do modułu TPM; kolejność szyfrowania nonce delegatów zaufania z EK, który jest niezmienny, do SRK, które można zmienić, jeśli nowy właściciel przejmuje na własność modułu TPM.

![Odszyfrowywanie nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Sprawdzanie poprawności żyli i odbierania poświadczeń

Urządzenie może następnie podpisać token sygnatury dostępu Współdzielonego przy użyciu odszyfrowanego urządzenia bnego i ponownie ustanowić połączenie z usługą inicjowania obsługi administracyjnej urządzeń przy użyciu podpisanego tokenu sygnatury dostępu Współdzielonego. Po zakończeniu wyzwania Nonce usługa umożliwia aprowizję urządzenia.

![Urządzenie przywraca połączenie z usługą inicjowania obsługi administracyjnej urządzeń w celu zweryfikowanie własności EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Następne kroki

Teraz urządzenie łączy się z Centrum IoT Hub i zapewniasz bezpieczeństwo, wiedząc, że klucze twoich urządzeń są bezpiecznie przechowywane. Teraz, gdy wiesz, jak usługa inicjowania obsługi administracyjnej urządzeń bezpiecznie weryfikuje tożsamość urządzenia za pomocą modułu TPM, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Dowiedz się więcej o wszystkich pojęciach dotyczących automatycznego inicjowania obsługi administracyjnej](./concepts-auto-provisioning.md)
* [Wprowadzenie do pracy przy użyciu automatycznego inicjowania obsługi administracyjnej](./quick-setup-auto-provision.md) przy użyciu sks do dbania o przepływ.
