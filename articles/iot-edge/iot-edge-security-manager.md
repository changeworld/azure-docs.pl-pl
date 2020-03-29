---
title: Menedżer zabezpieczeń usługi Azure IoT Edge — usługa Azure IoT Edge
description: Zarządza postawą zabezpieczeń urządzenia usługi IoT Edge i integralnością usług zabezpieczeń.
services: iot-edge
keywords: bezpieczeństwo, bezpieczny element, enklawa, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548700"
---
# <a name="azure-iot-edge-security-manager"></a>Menedżer zabezpieczeń usługi Azure IoT Edge

Menedżer zabezpieczeń usługi Azure IoT Edge jest dobrze powiązanym rdzeniem zabezpieczeń do ochrony urządzenia usługi IoT Edge i wszystkich jego składników przez abstrakcję bezpiecznego sprzętu krzemowego. Menedżer zabezpieczeń jest centralnym punktem utwardzania zabezpieczeń i zapewnia punkt integracji technologii producentom oryginalnego sprzętu (OEM).

![Menedżer zabezpieczeń usługi Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Menedżer zabezpieczeń usługi IoT Edge ma na celu ochronę integralności urządzenia Usługi IoT Edge i wszystkich nieodłącznych operacji oprogramowania. Menedżer zabezpieczeń przenosi zaufanie z źródłowego sprzętu głównego sprzętu zaufania (jeśli jest dostępny) do uruchamiania środowiska uruchomieniowego usługi IoT Edge i monitorowania bieżących operacji.  Menedżer zabezpieczeń usługi IoT Edge to oprogramowanie współpracujące z bezpiecznym sprzętem krzemowym (jeśli jest dostępne), aby zapewnić najwyższy możliwy poziom zabezpieczeń.  

Obowiązki menedżera zabezpieczeń ioT Edge obejmują między innymi:

* Zabezpieczone i zmierzone uruchamianie urządzenia usługi Azure IoT Edge.
* W stosownych przypadkach inicjowanie obsługi administracyjnej tożsamości urządzenia i przenoszenie zaufania.
* Hostuj i chroń składniki urządzeń usług w chmurze, takich jak usługa inicjowania obsługi urządzeń.
* Bezpiecznie aprowizuj moduły IoT Edge z unikatowymi tożsamościami.
* Gatekeeper do urządzenia sprzętu root zaufania za pośrednictwem usług notariusza.
* Monitorowanie integralności operacji usługi IoT Edge w czasie wykonywania.

Menedżer zabezpieczeń IoT Edge zawiera trzy składniki:

* Demon zabezpieczeń IoT Edge.
* Warstwa abstrakcji platformy zabezpieczeń sprzętowych (HSM PAL).
* Opcjonalny, ale wysoce zalecany sprzętowy krzemowy korzeń zaufania lub HSM.

## <a name="the-iot-edge-security-daemon"></a>Demon zabezpieczeń IoT Edge

Demon zabezpieczeń IoT Edge jest odpowiedzialny za logiczne operacje menedżera zabezpieczeń IoT Edge. Reprezentuje znaczną część zaufanej bazy obliczeniowej urządzenia Usługi IoT Edge.

### <a name="design-principles"></a>Zasady projektowania

Demon zabezpieczeń usługi IoT Edge jest zgodny z dwiema podstawowymi zasadami: maksymalizując integralność operacyjną i minimalizując nadęcie i churn.

#### <a name="maximize-operational-integrity"></a>Maksymalizacja integralności operacyjnej

Demon zabezpieczeń IoT Edge działa z najwyższą możliwą integralnością w ramach możliwości obrony danego sprzętu głównego zaufania. Dzięki odpowiedniej integracji główny sprzęt zaufania mierzy i monitoruje demona zabezpieczeń statycznie i w czasie wykonywania, aby oprzeć się manipulacjom.

Dostęp fizyczny jest zawsze zagrożeniem dla urządzeń IoT. Sprzętowy katalog główny zaufania odgrywa ważną rolę w obronie integralności demona zabezpieczeń usługi IoT Edge.  Sprzętowy korzeń zaufania jest dostępny w dwóch odmianach:

* zabezpieczają elementy do ochrony poufnych informacji, takich jak wpisy tajne i klucze kryptograficzne.
* bezpieczne enklawy do ochrony wpisów tajnych, takich jak klucze, i poufnych obciążeń, takich jak pomiary i rozliczenia.

Istnieją dwa rodzaje środowisk wykonywania, aby używać sprzętowego katalogu głównego zaufania:

* Standardowe lub bogate środowisko wykonywania (REE), które opiera się na użyciu bezpiecznych elementów w celu ochrony poufnych informacji.
* Zaufane środowisko wykonywania (TEE), które opiera się na użyciu technologii bezpiecznej enklawy w celu ochrony poufnych informacji i zapewnienia ochrony przed wykonywaniem oprogramowania.

W przypadku urządzeń używających bezpiecznych enklaw jako sprzętowego katalogu głównego zaufania, wrażliwa logika w demonie zabezpieczeń usługi IoT Edge powinna znajdować się wewnątrz enklawy.  Niewrażliwe fragmenty demona zabezpieczeń mogą znajdować się poza tee.  W każdym przypadku producenci oryginalnego projektu (ODM) i producenci oryginalnego sprzętu (OEM) powinni rozszerzyć zaufanie od swojego modułu HSM, aby zmierzyć i bronić integralności demona zabezpieczeń IoT Edge podczas rozruchu i wykonywania.

#### <a name="minimize-bloat-and-churn"></a>Minimalizowanie nadęcia i churn

Inną podstawową zasadą demona zabezpieczeń IoT Edge jest zminimalizowanie zmian.  Aby uzyskać najwyższy poziom zaufania, demon zabezpieczeń IoT Edge może ściśle łączyć się z katalogiem głównym zaufanych urządzeń i działać jako kod macierzysty.  Jest to typowe dla tego typu realizacji, aby zaktualizować oprogramowanie demona za pośrednictwem katalogu głównego sprzętu bezpiecznego ścieżki aktualizacji zaufania (w przeciwieństwie do mechanizmów aktualizacji dostarczonych przez system operacyjny), które mogą być trudne w niektórych scenariuszach.  Podczas gdy odnawianie zabezpieczeń jest zalecane dla urządzeń IoT, nadmierne wymagania dotyczące aktualizacji lub duże ładunki aktualizacji mogą rozszerzyć powierzchnię zagrożenia na wiele sposobów.  Przykłady obejmują pomijanie aktualizacji, aby zmaksymalizować dostępność operacyjną lub główny sprzęt zaufania zbyt ograniczone do przetwarzania dużych ładunków aktualizacji.  W związku z tym konstrukcja demona zabezpieczeń usługi IoT Edge jest zwięzła, aby zapewnić mały rozmiar i zaufaną bazę obliczeniową oraz zminimalizować wymagania dotyczące aktualizacji.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura demona zabezpieczeń IoT Edge

![Demon zabezpieczeń usługi Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Demon zabezpieczeń IoT Edge wykorzystuje wszystkie dostępne wzorce sprzętu technologii zaufania dla wzmocnienia zabezpieczeń.  Umożliwia również operację w trybie podziału świata między standardowym/bogatym środowiskiem wykonywania (REE) a zaufanym środowiskiem wykonywania (TEE), gdy technologie sprzętowe oferują zaufane środowiska wykonywania. Interfejsy specyficzne dla roli umożliwiają głównych składników usługi IoT Edge, aby zapewnić integralność urządzenia usługi IoT Edge i jego operacji.

#### <a name="cloud-interface"></a>Interfejs chmury

Interfejs chmury umożliwia demonowi zabezpieczeń usługi IoT Edge dostęp do usług w chmurze, takich jak komplementy w chmurze, do zabezpieczeń urządzenia, takich jak odnawianie zabezpieczeń.  Na przykład demon zabezpieczeń usługi IoT Edge obecnie używa tego interfejsu, aby uzyskać dostęp do [usługi inicjowania obsługi administracyjnej urządzeń](https://docs.microsoft.com/azure/iot-dps/) usługi Azure IoT Hub do zarządzania cyklem życia tożsamości urządzenia.  

#### <a name="management-api"></a>Interfejs API zarządzania

Demon zabezpieczeń usługi IoT Edge oferuje interfejs API zarządzania, który jest wywoływany przez agenta usługi IoT Edge podczas tworzenia/uruchamiania/zatrzymywania/usuwania modułu usługi IoT Edge. Demon zabezpieczeń przechowuje "rejestracje" dla wszystkich aktywnych modułów. Te rejestracje mapują tożsamość modułu na niektóre właściwości modułu. Na przykład te właściwości modułu obejmują identyfikator procesu (pid) procesu uruchomionego w kontenerze i skrót zawartości kontenera platformy docker.

Te właściwości są używane przez interfejs API obciążenia (opisane poniżej), aby sprawdzić, czy obiekt wywołujący jest autoryzowany dla akcji.

Interfejs API zarządzania jest uprzywilejowanym interfejsem API, wywoływanym tylko z agenta usługi IoT Edge.  Ponieważ demon zabezpieczeń IoT Edge bootstraps i uruchamia agenta IoT Edge, sprawdza, czy agent IoT Edge nie został zmodyfikowany, a następnie można utworzyć niejawną rejestrację dla agenta IoT Edge. Ten sam proces zaświadczania, którego używa interfejs API obciążenia, również ogranicza dostęp do interfejsu API zarządzania tylko do agenta usługi IoT Edge.

#### <a name="container-api"></a>Interfejs API kontenera

Interfejs API kontenera współdziała z systemem kontenerów używanym do zarządzania modułami, takimi jak Moby lub Docker.

#### <a name="workload-api"></a>Interfejs API obciążenia

Interfejs API obciążenia jest dostępny dla wszystkich modułów. Zapewnia dowód tożsamości, jako token podpisany z zakorzenionym modułem HSM lub certyfikat X509 i odpowiedni pakiet zaufania do modułu. Pakiet zaufania zawiera certyfikaty urzędu certyfikacji dla wszystkich innych serwerów, którym moduły powinny ufać.

Demon zabezpieczeń usługi IoT Edge używa procesu zaświadczania do ochrony tego interfejsu API. Gdy moduł wywołuje ten interfejs API, demon zabezpieczeń próbuje znaleźć rejestrację tożsamości. Jeśli się powiedzie, używa właściwości rejestracji do pomiaru modułu. Jeśli wynik procesu pomiaru jest zgodny z rejestracją, generowany jest nowy dowód tożsamości. Odpowiednie certyfikaty urzędu certyfikacji (pakiet zaufania) są zwracane do modułu.  Moduł używa tego certyfikatu do łączenia się z Centrum IoT, innymi modułami lub uruchamiania serwera. Gdy podpisany token lub certyfikat zbliża się do wygaśnięcia, jest odpowiedzialny za moduł, aby zażądać nowego certyfikatu.

### <a name="integration-and-maintenance"></a>Integracja i konserwacja

Firma Microsoft utrzymuje główną bazę kodu [demona zabezpieczeń usługi IoT Edge w witrynie GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalacja i aktualizacje

Instalacja i aktualizacje demona zabezpieczeń usługi IoT Edge są zarządzane za pośrednictwem systemu zarządzania pakietami systemu operacyjnego. Urządzenia usługi IoT Edge ze sprzętowym korzeniem zaufania powinny dodatkowo wzmocnić integralność demona, zarządzając jego cyklem życia za pośrednictwem systemów bezpiecznego zarządzania rozruchem i aktualizacjami. Producenci urządzeń powinni eksplorować te możliwości na podstawie ich odpowiednich możliwości urządzenia.

#### <a name="versioning"></a>Przechowywanie wersji

Środowisko wykonawcze IoT Edge śledzi i raportuje wersję demona zabezpieczeń IoT Edge. Wersja jest zgłaszana jako atrybut *runtime.platform.version* właściwości zgłaszanej właściwości modułu usługi IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Warstwa abstrakcji platformy modułu zabezpieczeń sprzętowych (HSM PAL)

Program HSM PAL wyodrębnia wszystkie elementy sprzętowe do ufności, aby odizolować dewelopera lub użytkownika IoT Edge od ich złożoności.  Zawiera kombinację interfejsów programowania aplikacji (API) i transdomenowych procedur komunikacyjnych, na przykład komunikacji między standardowym środowiskiem wykonywania a bezpieczną enklawą.  Rzeczywista implementacja programu HSM PAL zależy od określonego bezpiecznego sprzętu w użyciu. Jego istnienie umożliwia wykorzystanie praktycznie każdego bezpiecznego sprzętu krzemowego.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpiecz silikonowy korzeń sprzętu zaufania

Bezpieczny krzem jest niezbędny do zakotwiczenia zaufania wewnątrz sprzętu urządzenia IoT Edge.  Bezpieczny krzem jest w różnorodności, aby uwzględnić moduł TPM ( Trusted Platform Module), wbudowany secure element (eSE), ARM TrustZone, Intel SGX i niestandardowe bezpieczne technologie krzemowe.  Zaleca się korzystanie z bezpiecznego silikonowego korzenia zaufania do urządzeń, biorąc pod uwagę zagrożenia związane z fizyczną dostępnością urządzeń IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integracja i konserwacja menedżera zabezpieczeń usługi IoT Edge

Menedżer zabezpieczeń usługi IoT Edge ma na celu identyfikowanie i izolowanie składników, które chronią zabezpieczenia i integralność platformy usługi Azure IoT Edge w celu niestandardowego hartowania. Strony trzecie, takie jak producenci urządzeń, powinny korzystać z niestandardowych funkcji zabezpieczeń dostępnych wraz ze sprzętem urządzenia.  Zobacz następne kroki sekcji łącza, które pokazują, jak wzmocnić Menedżera zabezpieczeń Usługi Azure IoT z modułu TPM (Trusted Platform Module) na platformach Linux i Windows. W tych przykładach używa się oprogramowania lub wirtualnych modułów TPM, ale bezpośrednio stosuje się do korzystania z dyskretnych urządzeń TPM.  

## <a name="next-steps"></a>Następne kroki

Przeczytaj blog na [Zabezpieczanie inteligentnej krawędzi](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Tworzenie i aprowizyjowania [urządzenia usługi IoT Edge za pomocą wirtualnego modułu TPM na maszynie wirtualnej systemu Linux](how-to-auto-provision-simulated-device-linux.md).

Tworzenie i aprowizyjowania [urządzenia usługi IoT Edge za pomocą symulowanego modułu TPM w systemie Windows](how-to-auto-provision-simulated-device-windows.md).
