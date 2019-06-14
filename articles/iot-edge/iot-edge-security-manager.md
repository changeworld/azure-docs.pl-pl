---
title: Dowiedz się, jak Menedżer zabezpieczeń chroni urządzenia, oprogramowanie — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Zarządza wystąpienie zabezpieczeń urządzenia usługi IoT Edge i integralności usługi zabezpieczeń.
services: iot-edge
keywords: bezpieczeństwo, element bezpieczny, enklawy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bc441e2bbd36c8d078eb67aff48e58684a026289
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444986"
---
# <a name="azure-iot-edge-security-manager"></a>Menedżer zabezpieczeń w usłudze Azure IoT Edge

Menedżer zabezpieczeń usługi Azure IoT Edge jest podstawowa dobrze ograniczonych zabezpieczeń na potrzeby ochrony urządzenia usługi IoT Edge i wszystkich jego składników, zapewniając abstrakcyjność sprzętu dolina bezpieczne. Stanowi centralny punkt administracji dla zabezpieczeń i udostępnia punkt integracji technologii do oryginalnego producenci urządzeń (OEM).

![Menedżer zabezpieczeń w usłudze Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Menedżer zabezpieczeń usługi IoT Edge ma na celu ochrony integralności urządzenia usługi IoT Edge i wszystkie operacje używaniem oprogramowania.  Robi to przez przejście relację zaufania z bazowego sprzętu głównym zaufania sprzętu (jeśli dostępne) do bezpiecznego uruchamiania środowisko uruchomieniowe usługi IoT Edge i monitorowania integralności jej operacje w dalszym ciągu.  Menedżer zabezpieczeń usługi IoT Edge jest pracy oprogramowania wraz z silicon bezpiecznego sprzętu (jeśli dostępne) aby wspomóc dostarczanie najwyższej gwarancje bezpieczeństwa, możliwe.  

Obowiązki Menedżera zabezpieczeń usługi IoT Edge obejmuje, ale nie są ograniczone do:

* Zabezpieczone i mierzone ładowanie początkowe urządzenia usługi Azure IoT Edge.
* Aprowizacji tożsamości urządzenia i przejścia zaufania, jeśli ma to zastosowanie.
* Hostowanie i chronić składniki usług cloud Services, takich jak usługi Device Provisioning.
* Bezpiecznie aprowizuj moduły usługi IoT Edge z unikatowymi tożsamościami.
* Programu Gatekeeper do urządzeń sprzętowych elementu głównego zaufania za pośrednictwem usług notariusz.
* Monitoruje integralność operacji usługi IoT Edge w czasie wykonywania.

Menedżer zabezpieczeń usługi IoT Edge obejmuje trzy składniki:

* Demon zabezpieczeń usługi IoT Edge.
* Zabezpieczenia moduł platformy abstrakcją sprzętu warstwy (PAL przez sprzętowy moduł zabezpieczeń).
* Opcjonalne, ale zdecydowanie zalecany sprzęt dolina katalog główny zaufania lub sprzętowego modułu zabezpieczeń.

## <a name="the-iot-edge-security-daemon"></a>Demon zabezpieczeń usługi IoT Edge

Demon zabezpieczeń usługi IoT Edge to oprogramowanie, które są odpowiedzialne za operacje logiczne Menedżera zabezpieczeń usługi IoT Edge. Jest znaczna część zaufanych obliczeń podstawą urządzenie usługi IoT Edge. 

### <a name="design-principles"></a>Zasady projektowania

Demon zabezpieczeń usługi IoT Edge następujące dwie podstawowe zasady: zmaksymalizować operacyjnej integralności i zminimalizować rozrostu i współczynnika zmian.

#### <a name="maximize-operational-integrity"></a>Maksymalizuj integralności operacyjne

Demon zabezpieczeń usługi IoT Edge działa z najwyższą integralności w obrębie możliwości defense każdy główny danego sprzętu zaufania. Dzięki integracji z właściwego głównego zaufania sprzętu mierzy i monitoruje demona zabezpieczeń statycznie i w czasie wykonywania, chroniony przed naruszeniem.

Dostęp fizyczny jest zawsze zagrożeń na urządzeniach IoT. Sprzęt elementu głównego zaufania odgrywa ważną rolę w obronie integralności demona zabezpieczeń usługi IoT Edge.  Sprzęt elementu głównego zaufania są dostępne w dwóch wersjach w odpowiedzi:

* bezpieczne elementów do ochrony informacji poufnych, takich jak klucze tajne i klucze szyfrowania.
* bezpieczne eksterytorialne ochrony kluczy tajnych, takich jak klucze i poufnych obciążeń, takich jak pomiarów i rozliczeń.

Istnieją dwa rodzaje środowisk wykonawczych wykorzystanie sprzętu elementu głównego zaufania:

* Standardowy lub rozbudowane środowisko wykonawcze (REE) polegają na korzystanie z bezpiecznej elementy, aby chronić poufne informacje.
* Zaufane środowisko wykonawcze (TEE) polegają na korzystanie z technologii bezpiecznego enklawy chronić poufne informacje i oferować ochronę na uruchamianie oprogramowania.

W przypadku urządzeń przy użyciu bezpiecznego eksterytorialne sprzętu elementu głównego zaufania poufnych logiki w ramach usługi IoT Edge security demona oczekuje się, znajdują się w ramach enklawy.  Niewrażliwi części demona zabezpieczeń mogą znajdować się poza TEE.  W każdym przypadku oczekuje się oryginalny projekt producentów (ODM) i producentów oryginalnego sprzętu (OEM) do rozszerzenia relacji zaufania z ich modułu HSM do pomiaru i obrony integralności demona zabezpieczeń usługi IoT Edge przy rozruchu i środowiska uruchomieniowego.

#### <a name="minimize-bloat-and-churn"></a>Zminimalizować zasobożerności i współczynnika zmian

Inny podstawową zasadą demona zabezpieczeń usługi IoT Edge jest minimalizowanie postępu dokonanego w.  Najwyższy poziom zaufania demona zabezpieczeń usługi IoT Edge można ściśle Połącz z urządzeń sprzętowych elementu głównego zaufania i działają jako kodu natywnego.  Jest to częsty problem w tego rodzaju realizations do aktualizacji oprogramowania demona sprzętu głównym zaufania firmy zabezpieczonych aktualizacji ścieżek (w przeciwieństwie do systemu operacyjnego, pod warunkiem mechanizmy aktualizacji), który może być wyzwaniem w zależności od konkretnego scenariusza sprzętu i wdrażania.  Podczas odnawiania zabezpieczeń jest silne zalecenie dla urządzenia IoT, jego brzmieniu przyczyny, czy aktualizacja nadmierne wymagania lub ładunków dużej aktualizacji można rozwinąć powierzchni zagrożeń na wiele sposobów.  Przykłady obejmują pomijanie aktualizacji, aby zmaksymalizować dostępność operacyjną lub głównym zaufania sprzętu zbyt ograniczone do przetwarzania dużej aktualizacji ładunków.  W efekcie projekt demona zabezpieczeń usługi IoT Edge jest zwięzła, aby zachować śladu i dlatego zaufanych przetwarzania małych podstawowej oraz zminimalizować wymagania dotyczące aktualizacji.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura demona zabezpieczeń usługi IoT Edge

![Demon zabezpieczeń w usłudze Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Demon zabezpieczeń usługi IoT Edge została zaprojektowana, aby móc korzystać z dowolnego sprzętu głównym zaufania technologii zabezpieczeń.  Umożliwia ona również wartościach split-world środowiska wykonywania Standard/Zaawansowane (REE) i zaufanego środowiska wykonywania (TEE) podczas technologii sprzętowych oferują środowiska wykonawcze zaufanych. Interfejsy specyficzne dla ról umożliwiają wzajemnego oddziaływania głównych składników usługi IoT Edge, aby zapewnić integralność usługi IoT Edge, urządzenia i jego operacje.

#### <a name="cloud-interface"></a>Interfejs chmurowy

Interfejs chmurowy umożliwia dostęp do usług cloud services, takich jak gratulacjami chmury w celu zabezpieczenia urządzenia, takie jak zabezpieczenia odnowienia demona zabezpieczeń usługi IoT Edge.  Na przykład demon zabezpieczeń usługi IoT Edge aktualnie używa tego interfejsu do dostępu do usługi Azure IoT Hub [usługi aprowizacji urządzeń (DPS)](https://docs.microsoft.com/azure/iot-dps/) do zarządzania cyklem życia tożsamości urządzenia.  

#### <a name="management-api"></a>Interfejs API zarządzania

Demon zabezpieczeń usługi IoT Edge zapewnia interfejs API zarządzania, które jest wywoływane przez agenta usługi IoT Edge podczas tworzenia/uruchamianie/zatrzymywanie/usuwania moduł usługi edge. Demon zabezpieczeń usługi IoT Edge przechowuje "rejestracje" dla wszystkich modułów active. Tyto registrace modułu tożsamości są mapowane na niektórych właściwości modułu. Kilka przykładów tych właściwości są identyfikator procesu (pid) procesu uruchomionego w kontenerze lub skrót zawartość kontenerów platformy docker.

Te właściwości są używane przez obciążenie interfejsu API (opisanych poniżej), aby stwierdzać, że obiekt wywołujący jest autoryzowany do wykonania akcji.

Interfejs API zarządzania to API uprzywilejowanego, można wywołać tylko za pomocą agenta usługi IoT Edge.  Ponieważ demona zabezpieczeń usługi IoT Edge używa do ładowania i uruchamia agenta usługi IoT Edge, może utworzyć niejawne rejestracji agenta usługi IoT Edge po została potwierdzona, czy agent usługi IoT Edge nie zostały naruszone. Ten sam proces zaświadczania, używany przez obciążenie interfejsu API jest używany do ograniczania dostępu do interfejsu API zarządzania tylko agenta usługi IoT Edge.

#### <a name="container-api"></a>Kontener interfejsu API

Demon zabezpieczeń usługi IoT Edge zapewnia interfejs kontenera do interakcji z systemem kontenerów na użytek takich jak Moby i platformy Docker modułu podczas tworzenia wystąpienia.

#### <a name="workload-api"></a>Obciążenie interfejsu API

Obciążenie interfejsu API jest demona zabezpieczeń usługi IoT Edge interfejsu API dostępne dla wszystkich modułów, w tym agent usługi IoT Edge. Umożliwia potwierdzenie tożsamości, albo przez sprzętowy moduł zabezpieczeń dostępu do konta root podpisany token lub X509 certyfikatów i zaufania odpowiedniego pakietu do modułu. Pakiet zaufania zawiera certyfikaty urzędu certyfikacji dla wszystkich serwerów, które moduły należy ufać.

Demon zabezpieczeń usługi IoT Edge używa procesu zaświadczania, aby je przed nieprzewidzianymi tego interfejsu API. Gdy moduł wywołuje ten interfejs API, demon zabezpieczeń usługi IoT Edge próbuje znaleźć rejestracji dla tożsamości. Jeśli to się powiedzie, używa właściwości rejestracji do mierzenia modułu. Jeśli w wyniku procesu pomiaru pasuje do rejestracji, nowego modułu HSM dostęp do konta root podpisany token lub X509 wygenerowany certyfikat. Odpowiednie certyfikaty urzędu certyfikacji (pakiet zaufania) są zwracane do modułu.  Moduł używa tego certyfikatu do łączenia usługi IoT Hub, inne moduły lub uruchamiania serwera. Jeśli podpisany token lub certyfikat będzie bliski wygaśnięcia, spoczywa modułu do żądania nowego certyfikatu. 

### <a name="integration-and-maintenance"></a>Integracja i utrzymanie

Firma Microsoft udostępnia głównego kodu podstawowego dla [demona zabezpieczeń usługi IoT Edge w witrynie GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalacji i aktualizacji

Instalacji i aktualizacji usługi IoT Edge demona zabezpieczeń są zarządzane za pośrednictwem systemu zarządzania pakietami systemu operacyjnego. Urządzenia usługi IoT Edge przy użyciu sprzętu elementu głównego zaufania powinien zapewniać dodatkowe ograniczenie funkcjonalności integralność demona przez zarządzanie ich cyklem życia przy użyciu bezpiecznego rozruchu i systemów zarządzania aktualizacjami.  Pozostawia się urządzeń osoby podejmujące decyzje, aby zapoznać się z tymi drogi prowadzące zgodnie z ich odpowiednimi urządzeniami.

#### <a name="versioning"></a>Obsługa wersji

Środowisko uruchomieniowe usługi IoT Edge śledzi i zgłasza wersję demona zabezpieczeń usługi IoT Edge. Wersja jest zgłaszana jako *runtime.platform.version* atrybut moduł agenta usługi IoT Edge zgłoszonych właściwości.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Warstwa abstrakcji platformy modułu zabezpieczeń sprzętu (PAL przez sprzętowy moduł zabezpieczeń)

HSM PAL przenosi wszystkie głównym zaufania sprzętu do izolowania dewelopera lub użytkownika usługi IoT Edge z ich złożoności.  Składa się z kombinacji interfejsu programowania aplikacji (API) i trans procedury łączności domeny, na przykład komunikacji między środowisko standardowe wykonanie i bezpieczne enklawy.  Rzeczywiste wdrożenie PAL sprzętowego modułu zabezpieczeń zależy od konkretnego sprzętu bezpieczny w użyciu. Początku swojego istnienia umożliwia korzystanie z praktycznie dowolnego sprzętu dolina bezpieczne.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpieczenia głównego dolina sprzętu zaufania

Dolina bezpieczne jest kotwicy zaufania wewnątrz usługi IoT Edge urządzeń sprzętowych.  Dolina bezpiecznego są dostępne w różnych Trusted Platform Module (TPM), osadzonego elementu bezpieczne (eSE), ARM TrustZone, SGX firmy Intel i technologii niestandardowe dolina bezpieczne.  Korzystanie z bezpiecznej dolina elementu głównego zaufania na urządzeniach zdecydowanie zaleca się podane zagrożeniach związanych z fizycznie ułatwień dostępu z urządzeń IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integracja Menedżera zabezpieczeń usługi IoT Edge i konserwacja

Menedżer zabezpieczeń usługi IoT Edge ma na celu identyfikowania i izolowania składników, które ochrony bezpieczeństwa i integralności platformy usługi Azure IoT Edge dla niestandardowych zaostrzanie poziomu zabezpieczeń. Należy wprowadzić w innych firm, takich jak producenci urządzeń korzystać z funkcji zabezpieczeń niestandardowych jest dostępne z ich urządzeń sprzętowych.  Zobacz sekcji Następne kroki łączy, które pokazują, jak zabezpieczyć Menedżera zabezpieczeń usługi Azure IoT przy użyciu Trusted Platform Module (TPM) na platformach Windows i Linux. Te przykłady za pomocą oprogramowania lub wirtualne moduły TPM, ale dotyczą bezpośrednio za pomocą dyskretnych urządzenia modułu TPM.  

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z blogiem na [zabezpieczanie inteligentnych rozwiązań brzegowych](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Tworzenie i aprowizowanie [urządzenia usługi IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md).

Tworzenie i aprowizowanie [urządzenia usługi IoT Edge przy użyciu symulowanego modułu TPM na Windows](how-to-auto-provision-simulated-device-windows.md).
