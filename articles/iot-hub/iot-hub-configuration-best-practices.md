---
title: Konfiguracja urządzenia najlepsze rozwiązania dotyczące usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące konfigurowania urządzeń IoT na dużą skalę
author: chrisgre
ms.author: chrisgre
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 33e77d63b958df292ee9b4ac8ded41f3693cb6bc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485811"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Najlepsze rozwiązania dotyczące konfiguracji urządzenia w ramach rozwiązania IoT

Zarządzanie urządzeniami automatyczne w usłudze Azure IoT Hub automatyzuje wiele zadań powtarzających się i złożone zarządzania flot duże urządzenia względem całej ich cykle życia. W tym artykule definiuje wiele najlepszych rozwiązań dotyczących różnych ról zaangażowanych w rozwijania i obsługi rozwiązań IoT.

* **Producent sprzętu IoT/integrator:** Producentów sprzętu IoT integratorzy złożenia sprzętu od różnych producentów lub dostawców, zapewniając sprzętu do wdrożenia IoT wytwarzane lub zintegrowane za pośrednictwem innych dostawców. Zaangażowana w rozwój i integracja oprogramowania układowego, wbudowanych systemów operacyjnych i oprogramowania osadzonego.

* **Dla deweloperów rozwiązań IoT:** Tworzenie rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Dewelopera może być częścią wewnętrznego zespołu lub integrator systemów specjalizującym się w przypadku tego działania. Deweloper rozwiązania IoT mogą tworzenia różnych składników zestawu rozwiązań IoT od podstaw, zintegrować różne składniki standardowe lub typu open source lub dostosowywania [akcelerator rozwiązań IoT](/azure/iot-accelerators/).

* **Operator rozwiązania IoT:** Po wdrożeniu rozwiązania IoT wymaga długoterminowej obsługi, monitorowaniem, zarządzaniem uaktualnienia i konserwacji. Te zadania może odbywać się przez wewnętrznego zespołu, który składa się z operacji sprzętu i konserwacji zespołów, specjaliści ds. informacji o technologii i specjalistów domeny, które monitorują prawidłowe działanie całej infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Omówienie zarządzania urządzeniami automatyczne konfigurowanie urządzeń IoT na dużą skalę

Zarządzanie urządzeniami automatycznego obejmuje korzyści płynące ze [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) i [bliźniaczych reprezentacjach modułów](iot-hub-devguide-module-twins.md) do synchronizacji stanów żądaną i podać między chmurą i urządzeniami. [Konfiguracje urządzeń automatycznego](iot-hub-auto-device-config.md) automatycznie aktualizować dużych zestawów bliźniaczych reprezentacji i Podsumowanie postępu i zgodności. Następujące ogólne kroki opisują jak automatyczne zarządzanie urządzeniami jest rozwinięte i używać:

* **IoT, producent sprzętu/integrator** implementuje funkcje zarządzania urządzeniami w ramach aplikacji osadzonej przy użyciu [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md). Funkcje te mogą obejmować aktualizacje oprogramowania układowego, instalacja oprogramowania i aktualizacji i zarządzania ustawieniami.

* **Deweloperów rozwiązań IoT** implementuje warstwę zarządzania operacje zarządzania urządzeniami przy użyciu [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) i [konfiguracji urządzeń automatycznego](iot-hub-auto-device-config.md). Rozwiązanie powinno obejmować Definiowanie interfejsu operatora do wykonywania zadań zarządzania urządzeniami.

* **Operatora rozwiązania IoT** używa rozwiązania IoT, aby wykonywać zadania dotyczące zarządzania urządzeniami, szczególnie do grupowania urządzeń ze sobą, zainicjować zmian konfiguracji, takich jak aktualizacje oprogramowania układowego, monitorować postęp i rozwiązywanie problemów z problemów, które pojawiają się.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT, producent sprzętu/integrator

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu i rozwiązywania problemów związanych z projektowaniem oprogramowania osadzonego integratorzy:

* **Implementowanie [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md):** Bliźniacze reprezentacje urządzeń Włącz synchronizowanie wymaganą konfiguracją z chmury oraz raportowanie w bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem wdrożenia bliźniaczych reprezentacji urządzeń w ramach aplikacji embedded jest za pośrednictwem [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Bliźniacze reprezentacje urządzeń to najlepszym rozwiązaniem w przypadku konfiguracji, ponieważ ich:

    * Obsługuje komunikację dwukierunkową.
    * Umożliwia zarówno Stany urządzeń połączonych, jak i bez połączenia.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni możliwością tworzenia zapytań w chmurze.

* **Struktura bliźniaczej reprezentacji urządzenia do zarządzania urządzeniami:** Bliźniacza reprezentacja urządzenia powinny mieć strukturę w taki sposób, że właściwości zarządzania urządzeniami są logicznie pogrupowane na sekcje. Ten sposób umożliwi być izolowane, bez wywierania wpływu na inne sekcje bliźniaczej reprezentacji zmiany w konfiguracji. Na przykład można utworzyć części w obrębie żądane właściwości dla oprogramowania układowego, innej sekcji do oprogramowania oraz trzecią sekcję ustawień sieci. 

* **Raport atrybutów urządzenia, które są przydatne do zarządzania urządzeniami:** Atrybutów, takich jak urządzenia fizycznego, producent i model, oprogramowania układowego, system operacyjny, numer seryjny i inne identyfikatory są przydatne do raportowania i jako parametry dla przeznaczonych dla zmian w konfiguracji.

* **Definiowanie stanów głównego w celu raportowania stanu i postępu:** Stany najwyższego poziomu należy wyliczyć, dzięki czemu mogą zostać zgłoszone dla operatora. Na przykład aktualizacja oprogramowania układowego będzie zgłaszać stan jako bieżący, pobieranie, stosowanie, w toku i błędów. Na każdy stan, należy zdefiniować dodatkowe pola, aby uzyskać więcej informacji.

## <a name="iot-solution-developer"></a>Dla deweloperów rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów IoT, którzy tworzą systemy oparte na platformie Azure:

* **Implementowanie [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md):** Bliźniacze reprezentacje urządzeń Włącz synchronizowanie wymaganą konfiguracją z chmury oraz raportowanie w bieżącej konfiguracji i właściwości urządzenia. Najlepszym sposobem wdrożenia bliźniaczych reprezentacji urządzeń w ramach aplikacji rozwiązań w chmurze jest za pośrednictwem [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Bliźniacze reprezentacje urządzeń to najlepszym rozwiązaniem w przypadku konfiguracji, ponieważ ich:

    * Obsługuje komunikację dwukierunkową.
    * Umożliwia zarówno Stany urządzeń połączonych, jak i bez połączenia.
    * Postępuj zgodnie z zasadą spójności ostatecznej.
    * Są w pełni możliwością tworzenia zapytań w chmurze.

* **Organizowanie urządzeń za pomocą tagów bliźniaczych reprezentacji urządzeń:** Rozwiązanie powinno umożliwić operatora do definiowania pierścieni jakości lub inne zestawy urządzeń, w oparciu o różne strategie wdrażania, takie jak canary. Urządzenie organizacji można zaimplementować w ramach rozwiązania przy użyciu tagów bliźniaczych reprezentacji urządzeń i [zapytania](iot-hub-devguide-query-language.md). Organizacja urządzenia jest niezbędne w celu umożliwienia dla szczegółowymi informacjami dotyczącymi konfiguracji procesu, bezpieczne i dokładnie.

* **Implementowanie [konfiguracji urządzeń automatycznego](iot-hub-auto-device-config.md):** Konfiguracje urządzeń automatycznego wdrażania i konfiguracji monitora zmieni się na potrzeby dużych zestawów urządzeń IoT za pomocą bliźniaczych reprezentacji urządzeń.

   Konfiguracji automatycznego urządzeń docelowych zestawy bliźniacze reprezentacje urządzeń za pośrednictwem **warunek docelowy** która jest zapytaniem na urządzeniu tagów bliźniaczych reprezentacji lub zgłoszonych właściwości. **Dopasowywanie zawartości** ustawiono żądane właściwości ustawione w ciągu docelowym bliźniaczych reprezentacji urządzeń. Adresuj zawartość należy wyrównać ze strukturą bliźniaczej reprezentacji urządzenia, zdefiniowanych przez producenta sprzętu IoT/integrator. **Metryki** zapytań dotyczących bliźniaczej reprezentacji urządzenia zgłaszanych właściwości i również należy wyrównać ze strukturą bliźniaczej reprezentacji urządzenia, zdefiniowanych przez producenta sprzętu IoT/integrator.

   Konfiguracje urządzeń automatycznego uruchamiania po raz pierwszy wkrótce, po utworzeniu konfiguracji, a następnie co pięć minut. Mogą również korzystać z usługi IoT Hub, wykonywanie operacji bliźniaczej reprezentacji urządzenia z szybkością, który nigdy nie przekroczy [limity ograniczania przepływności](iot-hub-devguide-quotas-throttling.md) odczytów bliźniaczej reprezentacji urządzenia i aktualizacji.

* **Użyj [usługi Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** Deweloperzy rozwiązań należy używać usługi Device Provisioning przypisywanie tagów bliźniaczych reprezentacji urządzeń do nowych urządzeń, taki sposób, że zostaną one automatycznie skonfigurowane przez **konfiguracji urządzeń automatycznego** , są przeznaczone dla bliźniaczych elementów z tym tagiem. 

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące operatorom rozwiązania IoT przy użyciu rozwiązania IoT oparta na platformie Azure:

* **Organizowanie urządzenia do zarządzania:** Rozwiązania IoT, należy zdefiniować lub umożliwiają tworzenie sygnałów jakości lub inne zestawy urządzeń, w oparciu o różne strategie wdrażania, takie jak canary. Zestawy urządzeń będą używane w celu wprowadzania zmian w konfiguracji i wykonywanie innych operacji zarządzania urządzeniami na dużą skalę.

* **Wykonaj zmiany konfiguracji przy użyciu etapowego wdrażania:**  Etapowego wdrażania jest cały proces, według którego operator wdraża zmiany rozszerzanie zbiór urządzeń IoT. Celem jest, aby wprowadzić zmiany, stopniowo w celu zmniejszenia ryzyka dokonywania szerokiej skali istotne zmiany w.  Operator powinien umożliwia utworzenie interfejsu rozwiązania [konfiguracji urządzenia automatycznego](iot-hub-auto-device-config.md) i warunek docelowy powinien dotyczyć początkowego zestawu urządzeń (takich jak canary grupy). Operator powinien następnie zweryfikować zmiany konfiguracji w początkowego zestawu urządzeń.

   Po zakończeniu sprawdzania poprawności operator spowoduje zaktualizowanie konfiguracji na urządzenia automatycznego, aby uwzględnić większy zbiór urządzeń. Operator należy również ustawić priorytet dla konfiguracji mogą być wyższe niż inne konfiguracje, które mają obecnie zastosowania żadne do tych urządzeń. Roll out można monitorować przy użyciu metryk zgłoszone przez konfigurację urządzenia automatycznego.

* **Wykonaj wycofywanie zmian w razie błędów lub błędy konfiguracji:**  Konfiguracja urządzenia automatycznego, która powoduje występowanie błędów lub błędy konfiguracji można wycofać, zmieniając **przeznaczonych dla warunku** tak, aby urządzenia nie spełniają warunek określania wartości docelowej. Upewnij się, że inną konfigurację urządzenia automatycznego o niższym priorytecie nadal jest przeznaczona dla tych urządzeń. Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając metryki: Konfiguracji wycofane do tyłu już nie powinien być wyświetlony stan untargeted urządzeń, a druga Konfiguracja metryki powinny znajdować się teraz liczby urządzeń, które nadal są stosowane.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o implementacji bliźniaczych reprezentacji urządzeń w [poznawanie i używanie bliźniaczych reprezentacji urządzeń w usłudze IoT Hub](iot-hub-devguide-device-twins.md).

* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia konfiguracji urządzenia automatycznego w [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](iot-hub-auto-device-config.md).

* Implementowanie wzorca aktualizacji oprogramowania układowego, za pomocą bliźniaczych reprezentacji urządzeń i konfiguracji urządzenia automatycznego w [samouczka: Implementowanie proces aktualizacji oprogramowania układowego urządzenia](tutorial-firmware-update.md).
