---
title: Azure IoT Edge Security Manager — Azure IoT Edge
description: Zarządza wystąpienie zabezpieczeń urządzenia usługi IoT Edge i integralności usługi zabezpieczeń.
services: iot-edge
keywords: bezpieczeństwo, element bezpieczny, enklawy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548700"
---
# <a name="azure-iot-edge-security-manager"></a>Menedżer zabezpieczeń w usłudze Azure IoT Edge

Menedżer zabezpieczeń usługi Azure IoT Edge jest podstawowa dobrze ograniczonych zabezpieczeń na potrzeby ochrony urządzenia usługi IoT Edge i wszystkich jego składników, zapewniając abstrakcyjność sprzętu dolina bezpieczne. Menedżer zabezpieczeń jest punktem ogniskowym zabezpieczeń i zapewnia integrację technologii z producentem oryginalnego sprzętu (OEM).

![Menedżer zabezpieczeń w usłudze Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Menedżer zabezpieczeń usługi IoT Edge ma na celu ochrony integralności urządzenia usługi IoT Edge i wszystkie operacje używaniem oprogramowania. Usługa Security Manager przechodzi zaufania z bazowego sprzętu głównego sprzętu zaufania (jeśli jest dostępny) do uruchamiania IoT Edge środowiska uruchomieniowego i monitorowania bieżących operacji.  IoT Edge Security Manager to oprogramowanie działające wraz z bezpiecznym sprzętem krzemu (jeśli jest dostępny) w celu zapewnienia najwyższej możliwej gwarancji zabezpieczeń.  

Obowiązki programu IoT Edge Security Manager obejmują, ale nie są ograniczone do:

* Zabezpieczone i mierzone ładowanie początkowe urządzenia usługi Azure IoT Edge.
* Aprowizacji tożsamości urządzenia i przejścia zaufania, jeśli ma to zastosowanie.
* Hostowanie i chronić składniki usług cloud Services, takich jak usługi Device Provisioning.
* Bezpiecznie Zainicjuj obsługę modułów IoT Edge przy użyciu unikatowych tożsamości.
* Programu Gatekeeper do urządzeń sprzętowych elementu głównego zaufania za pośrednictwem usług notariusz.
* Monitoruje integralność operacji usługi IoT Edge w czasie wykonywania.

Menedżer zabezpieczeń usługi IoT Edge obejmuje trzy składniki:

* Demon zabezpieczeń usługi IoT Edge.
* Zabezpieczenia moduł platformy abstrakcją sprzętu warstwy (PAL przez sprzętowy moduł zabezpieczeń).
* Opcjonalne, ale zdecydowanie zalecany sprzęt dolina katalog główny zaufania lub sprzętowego modułu zabezpieczeń.

## <a name="the-iot-edge-security-daemon"></a>Demon zabezpieczeń usługi IoT Edge

Demon zabezpieczeń IoT Edge jest odpowiedzialny za operacje logiczne programu IoT Edge Security Manager. Reprezentuje znaczną część zaufanej bazy obliczeniowej na urządzeniu IoT Edge.

### <a name="design-principles"></a>Zasady projektowania

Demon zabezpieczeń IoT Edge jest zgodny z dwoma podstawowymi zasadami: Maksymalizuj integralność operacyjną i Minimalizuj przeładowanie i zmiany.

#### <a name="maximize-operational-integrity"></a>Maksymalizuj integralności operacyjne

Demon IoT Edge Security jest w stanie zapewnić najwyższą integralność w ramach funkcji obrony każdego danego elementu głównego sprzętu zaufania. Dzięki integracji z właściwego głównego zaufania sprzętu mierzy i monitoruje demona zabezpieczeń statycznie i w czasie wykonywania, chroniony przed naruszeniem.

Dostęp fizyczny jest zawsze zagrożeniem dla urządzeń IoT. Katalog główny sprzętu zaufania odgrywa ważną rolę w zabezpieczaniu integralności IoT Edge demona zabezpieczeń.  Katalog główny sprzętu zaufania ma dwie odmiany:

* bezpieczne elementów do ochrony informacji poufnych, takich jak klucze tajne i klucze szyfrowania.
* bezpieczne eksterytorialne ochrony kluczy tajnych, takich jak klucze i poufnych obciążeń, takich jak pomiarów i rozliczeń.

Istnieją dwa rodzaje środowisk wykonawczych do użycia głównego katalogu sprzętowego zaufania:

* Standardowe lub bogate środowisko wykonawcze (REE), które polega na użyciu bezpiecznych elementów do ochrony poufnych informacji.
* Środowisko TEE (Trusted Execution Environment), które polega na użyciu bezpiecznej technologii enklawy do ochrony poufnych informacji i zapewniania ochrony przed wykonaniem oprogramowania.

W przypadku urządzeń korzystających z bezpiecznego enclaves jako katalogu głównego sprzętu zaufania poufna logika w ramach IoT Edge Security DAEMON powinna znajdować się wewnątrz enklawy.  Niewrażliwe fragmenty demona zabezpieczeń mogą być poza TEEem.  W każdym przypadku pierwotni producenci projektu (ODM) i producenci oryginalnego sprzętu (OEM) powinni rozciągnąć zaufanie od ich modułu HSM, aby mierzyć i chronić integralność IoT Edge demona zabezpieczeń w przypadku rozruchu i środowiska uruchomieniowego.

#### <a name="minimize-bloat-and-churn"></a>Zminimalizować zasobożerności i współczynnika zmian

Inną podstawową zasadą dla demona IoT Edge Security jest zminimalizowanie zmian.  W przypadku najwyższego poziomu zaufania demon IoT Edge Security może ściśle współpracować z głównym katalogiem sprzętowym zaufania i działać jako kod natywny.  Jest to typowy dla tych typów realizacji, aby zaktualizować oprogramowanie demona za pomocą głównej ścieżki do sprzętu zaufanej aktualizacji (w przeciwieństwie do mechanizmów systemu operacyjnego dostarczonych przez system operacyjny), które mogą być trudne w niektórych scenariuszach.  Podczas gdy w przypadku urządzeń IoT zaleca się odnowienie zabezpieczeń, nadmierne wymagania dotyczące aktualizacji lub duże ładunki aktualizacji mogą rozszerzać powierzchnię zagrożeń na wiele sposobów.  Przykłady obejmują pomijanie aktualizacji, aby zmaksymalizować dostępność operacyjną lub głównym zaufania sprzętu zbyt ograniczone do przetwarzania dużej aktualizacji ładunków.  W związku z tym projekt demona IoT Edge Security jest zwięzły, aby zapewnić, że rozmiary i zaufanych obliczeń są niewielkie i zminimalizować wymagania dotyczące aktualizacji.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura demona zabezpieczeń usługi IoT Edge

![Demon zabezpieczeń w usłudze Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Demon zabezpieczeń IoT Edge korzysta z dowolnego dostępnego głównego sprzętu technologii zaufania na potrzeby zabezpieczania zabezpieczeń.  Umożliwia również dzielenie operacji na całym świecie między standardowym/rozbudowanym środowiskiem wykonywania (REE) a zaufanym środowiskiem wykonywania (TEE), gdy technologie sprzętowe oferują zaufane środowiska wykonawcze. Interfejsy specyficzne dla ról umożliwiają główne składniki IoT Edge, aby zapewnić integralność urządzenia IoT Edge i jego operacji.

#### <a name="cloud-interface"></a>Interfejs chmurowy

Interfejs chmury umożliwia demona IoT Edge Security, aby uzyskać dostęp do usług w chmurze, takich jak chmura odniesień do zabezpieczeń urządzeń, takich jak odnowienie zabezpieczeń.  Na przykład demon zabezpieczeń IoT Edge obecnie używa tego interfejsu do uzyskiwania dostępu do usługi Azure IoT Hub [Device Provisioning](https://docs.microsoft.com/azure/iot-dps/) w celu zarządzania cyklem życia tożsamości urządzeń.  

#### <a name="management-api"></a>Interfejs API zarządzania

IoT Edge Security DAEMON oferuje interfejs API zarządzania, który jest wywoływany przez agenta IoT Edge podczas tworzenia/uruchamiania/zatrzymywania/usuwania modułu IoT Edge. Demon zabezpieczeń zapisuje "rejestracje" dla wszystkich aktywnych modułów. Tyto registrace modułu tożsamości są mapowane na niektórych właściwości modułu. Przykładowo te właściwości modułów obejmują identyfikator procesu (PID) procesu działającego w kontenerze i skrót zawartości kontenera Docker.

Te właściwości są używane przez interfejs API obciążenia (opisany poniżej) w celu sprawdzenia, czy obiekt wywołujący jest autoryzowany do działania.

Interfejs API zarządzania to uprzywilejowany interfejs API, który można wywołać tylko z agenta IoT Edge.  Ponieważ IoT Edge Bootstrap demona zabezpieczeń i uruchamia agenta IoT Edge, sprawdza, czy Agent IoT Edge nie został naruszony, a następnie może utworzyć niejawną rejestrację dla agenta IoT Edge. Ten sam proces zaświadczania, którego używa interfejs API obciążenia, ogranicza dostęp do interfejsu API zarządzania tylko do agenta IoT Edge.

#### <a name="container-api"></a>Kontener interfejsu API

Interfejs API kontenera współdziała z systemem kontenerów używanym do zarządzania modułami, takimi jak Moby lub Docker.

#### <a name="workload-api"></a>Obciążenie interfejsu API

Interfejs API obciążenia jest dostępny dla wszystkich modułów. Zapewnia potwierdzenie tożsamości, jako certyfikat z podpisem własnym w formacie modułu HSM lub certyfikatu x509 oraz odpowiedni pakiet zaufania do modułu. Pakiet zaufania zawiera certyfikaty urzędu certyfikacji dla wszystkich pozostałych serwerów, które powinny być zaufane w modułach.

Demon zabezpieczeń IoT Edge używa procesu zaświadczania do zabezpieczenia tego interfejsu API. Gdy moduł wywołuje ten interfejs API, Demon zabezpieczeń próbuje znaleźć rejestrację dla tożsamości. Jeśli to się powiedzie, używa właściwości rejestracji do mierzenia modułu. Jeśli wynik procesu pomiaru jest zgodny z rejestracją, zostanie wygenerowana Nowa weryfikacja tożsamości. Odpowiednie certyfikaty urzędu certyfikacji (pakiet zaufania) są zwracane do modułu.  Moduł używa tego certyfikatu do łączenia usługi IoT Hub, inne moduły lub uruchamiania serwera. Gdy podpisany token lub certyfikat zbliża się do wygaśnięcia, odpowiedzialność za moduł żąda nowego certyfikatu.

### <a name="integration-and-maintenance"></a>Integracja i utrzymanie

Firma Microsoft udostępnia głównego kodu podstawowego dla [demona zabezpieczeń usługi IoT Edge w witrynie GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalacji i aktualizacji

Instalacja i aktualizacje demona IoT Edge Security DAEMON są zarządzane za pomocą system zarządzania pakietami systemu operacyjnego. Urządzenia IoT Edge z certyfikatem głównym sprzętu zaufania powinny zapewniać dodatkową ochronę przed integralnością demona przez Zarządzanie cyklem życia za pomocą systemów bezpiecznego rozruchu i zarządzania aktualizacjami. Twórcy urządzeń powinni poznać te drogi na podstawie ich możliwości urządzeń.

#### <a name="versioning"></a>Obsługa wersji

Środowisko uruchomieniowe usługi IoT Edge śledzi i zgłasza wersję demona zabezpieczeń usługi IoT Edge. Wersja jest raportowana jako atrybut *Runtime. platform. Version* dla właściwości "module agenta IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Warstwa abstrakcji platformy modułu zabezpieczeń sprzętu (PAL przez sprzętowy moduł zabezpieczeń)

HSM PAL przenosi wszystkie głównym zaufania sprzętu do izolowania dewelopera lub użytkownika usługi IoT Edge z ich złożoności.  Obejmuje on kombinację procedur komunikacji między interfejsem programowania aplikacji (API) i międzydomenowych, na przykład komunikację między standardowym środowiskiem wykonywania a bezpieczną enklawy.  Rzeczywiste wdrożenie PAL sprzętowego modułu zabezpieczeń zależy od konkretnego sprzętu bezpieczny w użyciu. Jego istnienie umożliwia korzystanie z praktycznie dowolnego bezpiecznego sprzętu krzemu.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpieczenia głównego dolina sprzętu zaufania

Dolina bezpieczne jest kotwicy zaufania wewnątrz usługi IoT Edge urządzeń sprzętowych.  Dolina bezpiecznego są dostępne w różnych Trusted Platform Module (TPM), osadzonego elementu bezpieczne (eSE), ARM TrustZone, SGX firmy Intel i technologii niestandardowe dolina bezpieczne.  Korzystanie z bezpiecznego modelu krzemu zaufania na urządzeniach jest zalecane z uwzględnieniem zagrożeń związanych z fizycznym dostępnością urządzeń IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integracja Menedżera zabezpieczeń usługi IoT Edge i konserwacja

IoT Edge Security Manager chce identyfikować i izolować składniki, które chronią zabezpieczenia i integralność platformy Azure IoT Edge w celu zapewnienia niestandardowej funkcjonalności. Osoby trzecie, takie jak twórcy urządzeń, powinny korzystać z niestandardowych funkcji zabezpieczeń dostępnych na urządzeniach.  Zobacz sekcję następne kroki, aby poznać linki, które pokazują, jak zabezpieczyć usługę Azure IoT Security Manager za pomocą moduł TPM (TPM) na platformach Linux i Windows. Te przykłady używają oprogramowania lub wirtualnej moduły TPM, ale bezpośrednio mają zastosowanie do korzystania z dyskretnych urządzeń TPM.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z blogiem na [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Utwórz i Udostępnij [urządzenie IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md).

Utwórz i Udostępnij [urządzenie IoT Edge przy użyciu symulowanego modułu TPM w systemie Windows](how-to-auto-provision-simulated-device-windows.md).
