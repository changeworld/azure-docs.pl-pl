---
title: Inicjowania obsługi usługi — moduł TPM zaświadczania urządzeń Centrum IoT Azure
description: Ten artykuł zawiera omówienie przepływu zaświadczania modułu TPM, za pomocą usługi udostępniania urządzenia IoT.
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: dec024c5c23bf8c628457127af57b8d18800660e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

Usługa inicjowania obsługi urządzeń Centrum IoT to usługa pomocnika do Centrum IoT, które są używane do konfigurowania urządzenia bezobsługową alokacji dla określonego Centrum IoT. Przy użyciu usługi inicjowania obsługi urządzeń można udostępnić milionów urządzeń w bezpieczny sposób.

W tym artykule opisano proces zaświadczanie o tożsamości, korzystając z [modułu TPM](./concepts-device.md). Moduł TPM oznacza moduł TPM i jest typem sprzętowego modułu zabezpieczeń (HSM). W tym artykule przyjęto założenie, używany jest odrębny, oprogramowania układowego lub zintegrowane modułu TPM. Oprogramowanie emulowanej moduły TPM są odpowiednie dla prototypowania lub testowania, ale ich nie zapewniają taki sam poziom zabezpieczeń jako odrębny, oprogramowania układowego lub czy zintegrowane moduły TPM. Nie zaleca się korzystanie z oprogramowania moduły TPM w środowisku produkcyjnym. [Dowiedz się więcej](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) o typach moduły TPM.

Ten artykuł dotyczy tylko urządzeń z HMAC Obsługa kluczy i kluczy poręczenia przy użyciu modułu TPM 2.0. Nie jest urządzeń przy użyciu certyfikatów X.509 do uwierzytelniania. Moduł TPM jest branżowym, normy ISO z Trusted Computing Group, a więcej dotyczące modułu TPM na [pełna specyfikacja 2.0 modułu TPM](https://trustedcomputinggroup.org/tpm-library-specification/) lub [specyfikacji ISO/IEC 11889](https://www.iso.org/standard/66510.html). W tym artykule przyjęto założenie, że znasz pary kluczy publicznych i prywatnych i jak są one używane do szyfrowania.

Urządzenie urządzenia inicjowania obsługi usługi SDK obsługi wszystko, co jest opisany w tym artykule. Nie istnieje potrzeba można zaimplementować żadnych dodatkowych czynności, jeśli używasz zestawy SDK na urządzeniach. Ten artykuł pomaga w zrozumieniu koncepcyjnie, co się dzieje z Twojej moduł TPM zabezpieczeń podczas przepisy Twojego urządzenia i dlaczego jest to bezpieczny.

## <a name="overview"></a>Przegląd

Moduły TPM Użyj coś o nazwie klucza poręczenia (EK) jako bezpieczne głównym zaufania. Klucz poręczenia jest unikatowy dla modułu TPM i zmianę zasadniczo zmienia urządzenia do nowej.

Istnieje inny typ klucza czy moduły TPM wywołana magazynu głównego klucza. SRK mogą być generowane przez właściciela modułu TPM, po jego właściciela modułu TPM. Przejmowanie na własność modułu TPM jest sposób specyficzne dla modułu TPM z informacją o tym "ktoś ustawia hasło w module HSM." Jeśli urządzenie modułu TPM jest oferowana nowego właściciela, nowy właściciel może przejąć na własność modułu TPM, aby wygenerować nowy SRK. Nowej generacji SRK gwarantuje, że poprzedniego właściciela, nie można użyć modułu TPM. Ponieważ SRK jest unikatowy dla właściciela modułu TPM, SRK może służyć do zapieczętowania danych do TPM tego właściciela. SRK zapewnia piaskownicy właściciela do przechowywania ich kluczy i zapewnia revocability dostępu, jeśli urządzenie lub moduł TPM jest sprzedawana. Przypomina to przeniesienie do nowy dom: przejmowanie na własność jest zmiana blokady na drzwi i niszczenie mebli wszystkich pozostałych przez poprzednie właścicieli (SRK), ale nie można zmienić adres DOM (EK).

Po skonfigurowaniu urządzenia i gotowa do użycia, będą dostępne zarówno EK i SRK dostępne do użycia.

![Przejmowanie na własność modułu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Jeden uwagi dotyczące przejmowania własności modułu TPM: przejmowania własności modułu TPM zależy od wielu czynników, w tym producenta modułu TPM, zestaw narzędzi modułu TPM, używany i systemu operacyjnego urządzenia. Postępuj zgodnie z instrukcjami odpowiednimi systemu do przejęcia na własność.

Usługa inicjowania obsługi urządzeń używa publicznej części klucza (EK_pub) do identyfikacji i rejestrowania urządzeń. Dostawcą urządzenia może odczytywać EK_pub podczas produkcji lub ostatecznego testowania i przekazać EK_pub do inicjowania obsługi administracyjnej usługi tak, aby urządzenia zostaną rozpoznane podczas łączenia się udostępnić. Usługa inicjowania obsługi urządzeń nie sprawdza SRK lub właściciela, więc "wyczyszczenie" dla modułu TPM na partycje powoduje usunięcie danych klienta, ale klucza (i innych danych dostawcy) zostaną zachowane i urządzenie zostanie rozpoznany przez usługę inicjowania obsługi urządzeń nadal podczas łączenia się udostępnić.

## <a name="detailed-attestation-process"></a>Szczegółowe zaświadczania procesu

Gdy urządzenie z modułem TPM pierwszy raz łączy się z usługą inicjowania obsługi urządzeń, usługa najpierw sprawdza podana EK_pub przed EK_pub przechowywane na liście rejestracji. Jeśli EK_pubs nie są zgodne, urządzenie nie może udostępnić. Jeśli EK_pubs są zgodne, usługa następnie wymaga urządzenia, aby potwierdzić prawa własności prywatny EK za pośrednictwem nonce wyzwania, które jest używane w celu potwierdzenia tożsamości wyzwaniem bezpiecznego. Usługa inicjowania obsługi urządzeń generuje identyfikatora jednorazowego i szyfruje go z SRK, a następnie EK_pub, które są udostępniane przez urządzenie podczas wywołania początkowe rejestracyjny. Moduł TPM zawsze przechowuje prywatny klucz poręczenia bezpieczne. To zapobiega fałszowania i gwarantuje, że tokeny sygnatury dostępu Współdzielonego są bezpiecznie udostępnione do autoryzowanych urządzeń.

Przejdźmy proces zaświadczania.

### <a name="device-requests-an-iot-hub-assignment"></a>Urządzenie żąda przypisania Centrum IoT

Najpierw urządzenie łączy się z usługą inicjowania obsługi urządzeń i żądania do udostępniania. W ten sposób urządzenie udostępnia usługę za pomocą jego Identyfikatora rejestracji, zakres Identyfikatora i EK_pub oraz SRK_pub z modułu TPM. Usługa przekazuje zaszyfrowany identyfikator jednorazowy urządzenia i prosi o urządzenia do odszyfrowania identyfikator jednorazowy i używać go do podpisywania tokenu sygnatury dostępu Współdzielonego, aby połączyć się ponownie i zakończyć inicjowanie obsługi.

![Inicjowanie obsługi żądań urządzeń](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Żądanie nonce

Urządzenie ma identyfikator jednorazowy i używa prywatnego części EK i SRK do odszyfrowania identyfikator jednorazowy do modułu TPM; Kolejność szyfrowania nonce delegatów zaufania od EK, którego nie można modyfikować, aby SRK, które można zmienić, jeśli nowy właściciel przejmuje modułu TPM.

![Identyfikator jednorazowy odszyfrowywania](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Weryfikowania identyfikatora jednorazowego i odbierać poświadczeń

Urządzenie można zarejestrować tokenu sygnatury dostępu Współdzielonego, za pomocą odszyfrowane identyfikator jednorazowy i ponownie ustanowić połączenie usługi inicjowania obsługi urządzeń przy użyciu podpisanego tokenu sygnatury dostępu Współdzielonego. Z żądaniem identyfikator jednorazowy ukończone usługa umożliwia urządzeniu udostępniania.

![Urządzenia powoduje ponowne ustanowienie połączenia z punktu dystrybucji, aby zweryfikować własność EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz łączy urządzenie z Centrum IoT i umieszczeniu bezpiecznego w bazie wiedzy klucze Twojego urządzenia są bezpiecznie przechowywane. Teraz, gdy wiesz, jak usługa inicjowania obsługi urządzeń bezpiecznie weryfikuje tożsamość urządzenia za pomocą modułu TPM, zobacz następujące artykuły, aby dowiedzieć się więcej:

* [Więcej informacji na temat wszystkich pojęcia związane z automatycznego inicjowania obsługi administracyjnej](./concepts-auto-provisioning.md)
* [Rozpoczynanie pracy przy użyciu automatycznego inicjowania obsługi administracyjnej](./quick-setup-auto-provision.md) korzystanie z zestawów SDK automatyzującą przepływu.
