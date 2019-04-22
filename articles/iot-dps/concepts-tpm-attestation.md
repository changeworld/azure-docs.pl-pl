---
title: Azure IoT Hub Device Provisioning Service — zaświadczenia modułu TPM
description: Ten artykuł zawiera omówienie pojęć dotyczących przepływu zaświadczania TPM za pomocą usługi IoT Device Provisioning Service.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048881"
---
# <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

IoT Hub Device Provisioning Service to usługa pomocnika usługi IoT Hub, możesz użyć do skonfigurowania urządzenia i bezobsługowa aprowizacja w określonej usługi IoT hub. Przy użyciu usługi Device Provisioning można udostępnić z milionów urządzeń w bezpieczny sposób.

W tym artykule opisano proces zaświadczania tożsamości, korzystając z [modułu TPM](./concepts-device.md). Moduł TPM to Trusted Platform Module i jest typem sprzętowego modułu zabezpieczeń (HSM). W tym artykule przyjęto założenie, korzystają z kolumną dyskretną, oprogramowania układowego lub zintegrowane modułu TPM. Oprogramowanie TPM emulowanych są odpowiednie dla tworzenia prototypów lub testowania, ale ich nie zapewnia ten sam poziom zabezpieczeń jak dyskretnych, oprogramowania układowego lub czy zintegrowane moduły TPM. Firma Microsoft nie zaleca się korzystanie z oprogramowania moduły TPM w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat typów modułów TPM, zobacz [krótkie wprowadzenie do modułu TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Ten artykuł ma zastosowanie tylko dla urządzeń z kluczem HMAC Obsługa kluczy i kluczy poręczenia przy użyciu modułu TPM w wersji 2.0. Nie jest w przypadku urządzeń za pomocą certyfikatów X.509 do uwierzytelniania. Moduł TPM jest całej branży, standard ISO z Trusted Computing Group i możesz dowiedzieć się więcej o modułu TPM na [pełną specyfikacji TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) lub [specyfikacji ISO/IEC 11889](https://www.iso.org/standard/66510.html). W tym artykule przyjęto założenie, że możesz zapoznać się z pary kluczy publicznych i prywatnych i jak są używane do szyfrowania.

Urządzenia usługi Device Provisioning zestawy SDK obsługują wszystko, co jest opisane w tym artykule dla Ciebie. Nie ma potrzeby można zaimplementować nic więcej, korzystając z zestawów SDK na swoich urządzeniach. Ten artykuł pomaga zrozumieć pod względem koncepcyjnym, co się dzieje z modułem TPM zabezpieczeń podczas postanowienia swoje urządzenia i dlaczego jest to bezpieczny.

## <a name="overview"></a>Omówienie

Moduły TPM Użyj coś, co jest nazywane poręczenia klucza (EK) jako bezpieczne elementu głównego zaufania. Klucz poręczenia jest unikatowy dla modułu TPM, a następnie zmieniając go zasadniczo zmiany urządzenia do nowej.

Istnieje inny typ klucza, że moduły TPM nazwę magazynu głównego klucza. SRK mogą być generowane przez właściciela modułu TPM, po przejmuje na własność modułu TPM. Przejmowanie na własność modułu TPM jest sposób specyficzny dla modułu TPM, z informacją o tym "osoba ustawia hasło w ramach sprzętowego modułu zabezpieczeń." Jeśli urządzenie TPM jest sprzedawany do nowego właściciela, nowego właściciela może przejąć na własność modułu TPM, aby wygenerować nowy SRK. Nowa generacja SRK gwarantuje, że poprzedni właściciel nie można użyć modułu TPM. Ponieważ SRK jest unikatowy dla właściciela modułu TPM, SRK może służyć do zapieczętowania danych do samego modułu TPM dla tego właściciela. SRK zapewnia piaskownicy dla właściciela do przechowywania swoich kluczy i zawiera revocability dostępu, jeśli urządzenie lub moduł TPM jest oferowana. Jest on podobny do przenoszenia do nowego DOM: przejmowanie na własność jest zmiana blokady drzwi i zniszczenie wszystkich meble pozostawiony przez poprzedniego właścicieli (SRK), ale nie można zmienić adres DOM (EK).

Gdy urządzenie zostało skonfigurowane i gotowe do użycia, będzie miał klucz poręczenia i SRK, które są dostępne do użycia.

![Przejmowanie na własność modułu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

OneNote na przejmowanie na własność modułu TPM: Przejmowanie na własność modułu TPM, zależy od wiele rzeczy, w tym producenta modułu TPM, zestaw funkcji wspomagających modułu TPM używane i systemu operacyjnego urządzenia. Postępuj zgodnie z instrukcjami odpowiednimi do systemu, przejęcie na własność.

Usługi Device Provisioning Service używa publiczną część EK (EK_pub) do identyfikowania i rejestrowania urządzeń. Dostawca urządzenia może odczytywać EK_pub podczas produkcji lub finalnych testów i przesłanie EK_pub do usługi aprowizacji, które urządzenie zostanie rozpoznana podczas łączenia z aprowizacji. Usługi Device Provisioning Service nie sprawdza SRK ani właścicielem, dzięki czemu "wyczyszczenie" modułu TPM na partycje powoduje usunięcie danych klienta, ale klucz poręczenia (i inne dane dostawcy) są zachowywane i urządzenia nadal będzie zostanie rozpoznany przez usługę Device Provisioning, kiedy łączy do aprowizowania.

## <a name="detailed-attestation-process"></a>Proces zaświadczania szczegółowe

Przy pierwszym połączeniu urządzenia z modułem TPM w usłudze Device Provisioning, usługa najpierw sprawdza podana EK_pub względem EK_pub przechowywane na liście rejestracji. Jeśli EK_pubs nie są zgodne, urządzenie nie może aprowizować. Jeśli EK_pubs są zgodne, usługa następnie wymaga urządzenie, aby udowodnić własność prywatna część EK za pośrednictwem wezwanie jednorazowy jest bezpieczne wyzwaniem służy do potwierdzania tożsamości. Usługi Device Provisioning generuje identyfikatora jednorazowego i następnie szyfruje za pomocą SRK, a następnie EK_pub, które są dostarczane przez urządzenie podczas wywołania wstępnej rejestracji. Moduł TPM zawsze chroni część klucza prywatnego. To uniemożliwia fałszowanie i gwarantuje, że tokeny sygnatur dostępu Współdzielonego są bezpiecznie udostępnione do autoryzowanych urządzeń.

Przejdźmy teraz przez proces zaświadczania, szczegółowo.

### <a name="device-requests-an-iot-hub-assignment"></a>Urządzenie zażąda przypisania usługi IoT Hub

Najpierw urządzenie łączy się z usługą Device Provisioning i żądania aprowizacji. W ten sposób, urządzenie udostępni usługi z jego identyfikator rejestracji, zakres identyfikatorów EK_pub i SRK_pub z modułu TPM. Usługa przekazuje zaszyfrowany identyfikator jednorazowy urządzenia i prosi urządzenia do odszyfrowania identyfikator jednorazowy i używać go do podpisywania tokenu sygnatury dostępu Współdzielonego, aby ponownie nawiąż połączenie i zakończyć aprowizację.

![Urządzenie żądania aprowizacji](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Jednorazowy challenge

Urządzenie ma identyfikator jednorazowy i używa prywatnego części EK i SRK do odszyfrowania identyfikator jednorazowy do modułu TPM; kolejność zaufania delegatów nonce szyfrowania z klucz poręczenia, który jest niezmienny, SRK, które można zmienić, jeśli nowy właściciel przejmuje na własność modułu TPM.

![Identyfikator jednorazowy odszyfrowywania](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Weryfikowania identyfikatora jednorazowego i odbierania poświadczeń

Urządzenie można utworzyć token sygnatury dostępu Współdzielonego, za pomocą jednorazowego odszyfrowane i ponownie ustanów połączenie z usługą Device Provisioning przy użyciu podpisany token sygnatury dostępu Współdzielonego. Z żądaniem jednorazowego ukończone umożliwia urządzenia na potrzeby aprowizacji.

![Urządzenie ustanawia ponownie połączenie usługi Device Provisioning Service można zweryfikować własność EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz łączenia urządzenia z usługi IoT Hub i przytrzymasz bezpieczne w bazie wiedzy, klucze Twoje urządzenia są bezpiecznie przechowywane. Teraz, gdy wiesz, jak usługi Device Provisioning Service bezpiecznie weryfikuje tożsamość urządzenia przy użyciu modułu TPM, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Dowiedz się więcej o wszystkich koncepcje w automatycznej aprowizacji](./concepts-auto-provisioning.md)
* [Rozpoczęcie korzystania z automatycznego aprowizowania](./quick-setup-auto-provision.md) za pomocą zestawów SDK, aby zadbać o przepływ.
