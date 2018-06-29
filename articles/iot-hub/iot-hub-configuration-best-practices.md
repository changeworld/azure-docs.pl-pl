---
title: Konfiguracja urządzenia najlepsze rozwiązania dotyczące Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat najlepszych rozwiązań dotyczących konfigurowania urządzenia IoT na dużą skalę
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036007"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Najlepsze rozwiązania w zakresie konfiguracji urządzeń w ramach rozwiązania IoT

Zarządzanie urządzeniami automatyczne w Centrum IoT Azure automatyzuje wiele powtarzających się i złożone zadania związane z zarządzaniem floty dużych urządzenia za pośrednictwem całości ich cyklów. W tym artykule definiuje wiele najlepszych rozwiązań dla różnych ról zaangażowane w tworzenia i obsługi rozwiązania IoT.

* **Producenta sprzętu IoT/integrator:** sprzętu producentów IoT, integratory zebrania sprzętu od różnych producentów lub dostawców, zapewniając sprzętu wdrożenia IoT wyprodukowanych lub zintegrować przez innych dostawców. Zaangażowane w rozwoju i integracja z oprogramowania układowego, osadzonych systemów operacyjnych i oprogramowania osadzonych.
* **Deweloper rozwiązania IoT:** Projektowanie rozwiązania IoT jest zazwyczaj wykonywane przez dewelopera rozwiązania. Tego projektanta, może być częścią zespołu wewnętrznych lub systemu polega zajmujących się tego działania. Developer rozwiązania IoT można utworzyć różne składniki rozwiązania IoT od początku, integracji różnych składników standard lub open source lub dostosować [akceleratora rozwiązania IoT][lnk-solution].
* **Operator rozwiązania IoT:** rozwiązania IoT — po wdrożeniu, wymaga długoterminowej obsługi, monitorowaniem, uaktualnienia i konserwacji. Te zadania można wewnętrznych zespół, który składa się z informacji specjalistów technologii, operacje sprzętu i zespoły konserwacji i specjalistów domeny, które monitorują poprawne zachowanie całej infrastruktury IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Zrozumienie zarządzania urządzeniami automatyczne konfigurowanie urządzeń IoT na dużą skalę

Zarządzanie urządzeniami automatyczne zawiera wiele zalet [twins urządzenia] [ lnk-device-twins] i [twins modułu] [ lnk-module-twins] zsynchronizować potrzeby i zgłoszony stanów między chmurą i urządzeniami.  [Konfiguracje urządzenia automatycznego] [ lnk-auto-device-config] automatycznie zaktualizować dużych zestawów twins i summerize postęp i zgodności. Następujące ogólne kroki opisują sposób automatyczne zarządzanie urządzeniami jest opracowany i używać:

* **Producenta sprzętu IoT/integrator** implementuje funkcje zarządzania urządzeniami w osadzonych aplikacji przy użyciu [twins urządzenia][lnk-device-twins]. Funkcje te mogą obejmować aktualizacje oprogramowania układowego, instalacja oprogramowania i aktualizacji oraz zarządzania ustawieniami.
* **Developer rozwiązania IoT** implementuje warstwa zarządzania operacji zarządzania urządzeniami przy użyciu [twins urządzenia] [ lnk-device-twins] i [automatyczne urządzenia konfiguracje][lnk-auto-device-config]. Rozwiązanie obejmuje Definiowanie interfejsu operator do wykonywania zadań zarządzania urządzeniami.
* **Operator rozwiązania IoT** używa jednocześnie wykonywać zadania dotyczące zarządzania urządzeniami, szczególnie do grupy urządzeń, zainicjować zmian konfiguracji, takich jak aktualizacje oprogramowania układowego, monitorowania postępu i rozwiązywanie problemów z rozwiązania IoT problemów wystąpić.

## <a name="iot-hardware-manufacturerintegrator"></a>Producenta sprzętu IoT/integrator

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu i integratory zajmujących się rozwoju oprogramowania embedded:

* **Implementowanie [twins urządzenia][lnk-device-twins]:** twins urządzenia Włącz synchronizowanie żądaną konfiguracją w chmurze oraz raportowanie bieżącej właściwości konfiguracji i urządzenia.  Najlepszym sposobem wykonania twins urządzenia w aplikacji embedded odbywa się za pośrednictwem [Azure IoT SDK][lnk-azure-sdk].  Twins urządzenia są najbardziej odpowiednie dla konfiguracji, ponieważ ich:. obsługuje komunikację dwukierunkową b. Zezwalaj na dla obu urządzeń połączonych i niepołączonych c Stany. Postępuj zgodnie z zasadą spójność ostateczna d. są w pełni kolejność w chmurze
* **Konstrukcja dwie urządzenia do zarządzania urządzeniami:** dwie urządzenia ma być struktura w taki sposób, że właściwości zarządzania urządzenia są logicznie pogrupowane na sekcje. W ten sposób umożliwi odizolowanie bez wpływu na pozostałe sekcje zasad dwie zmiany w konfiguracji. Na przykład utworzyć sekcji w ramach żądanej właściwości dla innej sekcji w przypadku oprogramowania układowego i innej sekcji dla ustawień sieci. 
* **Raport atrybutów urządzenia, które są przydatne do zarządzania urządzeniami:** atrybutów, takich jak urządzenia fizycznego producent i model, oprogramowania układowego, systemu operacyjnego, numer seryjny i inne identyfikatory są przydatne do raportowania i jako parametrów do użycia z zmiany konfiguracji.
* **Zdefiniuj stanów głównego w celu raportowania stan i postęp:** stanów najwyższego poziomu należy wyliczyć, dzięki czemu mogą być zgłaszane do operatora. Na przykład stan bieżący, pobieranie i stosowanie, w toku oraz błąd rozpoczną przesyłanie raportów o aktualizacji oprogramowania.  Na każdy stan zdefiniować dodatkowe pola, aby uzyskać więcej informacji.  

## <a name="iot-solution-developer"></a>Deweloper rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązania IoT, którzy budowania systemów opartych na platformie Azure:

* **Implementowanie [twins urządzenia][lnk-device-twins]:** twins urządzenia Włącz synchronizowanie żądaną konfiguracją w chmurze oraz raportowanie bieżącej właściwości konfiguracji i urządzenia.  Najlepszym sposobem wykonania twins urządzenia w aplikacji rozwiązań w chmurze jest za pośrednictwem [Azure IoT SDK][lnk-azure-sdk].  Twins urządzenia są najbardziej odpowiednie dla konfiguracji, ponieważ ich:. obsługuje komunikację dwukierunkową b. Zezwalaj na dla obu urządzeń połączonych i niepołączonych c Stany. Postępuj zgodnie z zasadą spójność ostateczna d. są w pełni kolejność w chmurze
* **Organizować urządzenia przy użyciu tagów dwie urządzenia:** rozwiązanie powinno umożliwić operator do definiowania pierścieni jakości lub innych zestawów urządzeń według różnych strategii wdrażania, takie jak element canary. Urządzenie organizacji można zaimplementować w ramach rozwiązania przy użyciu tagów dwie urządzenia i [zapytania][lnk-queries].  Organizacja urządzeń jest niezbędne w celu umożliwienia dla dokumentów Wycofywanie konfiguracji bezpiecznie i dokładnie.
* **Implementowanie [konfiguracji automatycznego urządzeń][lnk-auto-device-config]:** konfiguracji urządzeń automatycznego wdrażania i zmiany konfiguracji monitora do dużych zestawów urządzenia IoT za pośrednictwem twins urządzenia.  Konfiguracji automatycznego urządzeń docelowych zestawy twins urządzenia za pośrednictwem **target warunku,** które jest zapytaniem na urządzeniu tagi dwie lub zgłosił właściwości. **Kierować zawartość** zestaw żądaną właściwości, które zostaną ustawione w twins urządzenia docelowego. Kierowanie zawartości powinno odpowiadać struktury dwie urządzenia zdefiniowanych przez producenta sprzętu IoT/integrator.  **Metryki** są zapytania na dwie urządzenia zgłoszone właściwości i powinno również odpowiadać struktury dwie urządzenia zdefiniowanych przez producenta sprzętu IoT/integrator. Konfiguracje automatyczne urządzenia mają również asysty Centrum IoT operacji dwie urządzenia z szybkością nigdy nie przekroczy [ograniczenie] [ lnk-throttling] odczyty dwie urządzeń i aktualizacji.
* **Użyj [usługi inicjowania obsługi urządzeń][lnk-dps]:** deweloperzy rozwiązań należy używać usługi inicjowania obsługi urządzeń można przypisać znaczniki dwie urządzenia do nowych urządzeń, taki sposób, że zostaną one automatycznie skonfigurowane przez **konfiguracji automatycznego urządzeń** przedmiotem twins z tym znacznikiem. 

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące operatorom rozwiązania IoT przy użyciu rozwiązania IoT zbudowany na platformie Azure:

* **Organizowanie urządzenia do zarządzania:** rozwiązania IoT należy zdefiniować lub umożliwić tworzenie pierścieni jakości lub innych zestawów urządzeń według różnych strategii wdrażania, takie jak element canary. Zestawy urządzeń będzie służyć wprowadzana zmian konfiguracji i wykonywanie innych operacji zarządzania urządzeniami na dużą skalę.
* **Dokonać zmian konfiguracji przy użyciu etapowe zbiorczego limit:** etapowe zbiorczego limit jest ogólny proces, zgodnie z którymi operator wdraża zmiany rozszerzanie zbiór urządzeń IoT. Celem jest zmiany stopniowo, aby zmniejszyć ryzyko dokonywania szerokości skali fundamentalne zmiany.  Operator powinien umożliwiają utworzenie rozwiązania interfejsu [konfiguracji automatycznego urządzenia][lnk-auto-device-config], i stan docelowy powinien docelowych początkowego zestawu urządzeń (takich jak grupy mozgi).  Operator następnie należy zweryfikować zmianę konfiguracji początkowej zbiór urządzeń.  Po zakończeniu sprawdzania operator spowoduje zaktualizowanie konfiguracji automatycznego urządzenia, aby uwzględnić większy zestaw urządzeń. Operator należy również ustawić priorytet konfiguracji wyższe niż inne konfiguracje, które obecnie wskazywane na tych urządzeniach.  Wycofaj limit można monitorować za pomocą metryki zgłoszone przez konfigurację automatycznego urządzenia. 
* **Wykonaj wycofań w przypadku błędów lub błędy konfiguracji:** konfiguracji automatycznego urządzenia, która powoduje występowanie błędów lub błędy konfiguracji można wycofać, zmieniając **przeznaczonych dla warunku** , aby urządzenia nie już spełnia warunek określania wartości docelowej.  Upewnij się, że innej konfiguracji urządzenia automatycznego niższego priorytetu nadal jest przeznaczony dla tych urządzeń.  Sprawdź, czy wycofywanie zakończyło się pomyślnie, wyświetlając metryki: Konfiguracja wycofana Wstecz nie powinien być wyświetlony stan untargeted urządzeń i metryki drugi konfiguracji teraz powinna zawierać liczbę urządzeń, których celem jest nadal.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o implementacji twins urządzenia w [opis i użyj twins urządzenie w Centrum IoT][lnk-device-twins]
* Szczegółowe kroki umożliwiające tworzenie, aktualizowanie lub usuwanie konfiguracji automatycznego urządzenia w [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę][lnk-auto-device-config].
* Implementowanie wzorca aktualizacji oprogramowania układowego przy użyciu twins urządzenia i konfiguracji urządzeń automatyczne w [samouczek: należy wdrożyć proces aktualizacji oprogramowania układowego urządzenia][lnk-firmware-update].

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/