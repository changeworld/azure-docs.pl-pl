---
title: Praktyki dotyczące zabezpieczeń dla producentów — usługa inicjowania obsługi administracyjnej urządzeń Usługi Azure IoT
description: Omówi typowe praktyki zabezpieczeń dla producentów OEM i urządzeń, którzy przygotowują urządzenia do zarejestrowania się w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529522"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Praktyki dotyczące zabezpieczeń dla producentów urządzeń IoT platformy Azure
Ponieważ coraz więcej producentów zwalnia urządzenia IoT, warto zidentyfikować wskazówki dotyczące typowych praktyk. W tym artykule podsumowano zalecane praktyki zabezpieczeń, które należy wziąć pod uwagę podczas wytwarzania urządzeń do użytku z usługą inicjowania obsługi administracyjnej urządzeń usługi Azure IoT (DPS).  

> [!div class="checklist"]
> * Wybieranie opcji uwierzytelniania urządzenia
> * Instalowanie certyfikatów na urządzeniach IoT
> * Integracja modułu TPM (Trusted Platform Module) z procesem produkcyjnym

## <a name="selecting-device-authentication-options"></a>Wybieranie opcji uwierzytelniania urządzenia
Ostatecznym celem każdego środka bezpieczeństwa urządzenia IoT jest stworzenie bezpiecznego rozwiązania IoT. Jednak problemy, takie jak ograniczenia sprzętowe, koszty i poziom wiedzy na temat zabezpieczeń, mają wpływ na opcje, które wybierzesz. Ponadto twoje podejście do zabezpieczeń wpływa na sposób, w jaki urządzenia IoT łączą się z chmurą. Chociaż istnieje [kilka elementów zabezpieczeń IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) do rozważenia, kluczowym elementem, który napotka każdego klienta jest typ uwierzytelniania do użycia. 

Trzy powszechnie stosowane typy uwierzytelniania to certyfikaty X.509, moduły zaufanej platformy (TPM) i klucze symetryczne. Chociaż istnieją inne typy uwierzytelniania, większość klientów, którzy twórz rozwiązania na platformie Azure IoT używać jednego z tych trzech typów. Poniżej dalsza część artykułu Ankieta zalet i wad korzystania z każdego typu uwierzytelniania.

### <a name="x509-certificate"></a>Certyfikat X.509
Certyfikaty X.509 to rodzaj tożsamości cyfrowej, której można używać do uwierzytelniania. Standard certyfikatu X.509 jest udokumentowany w [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). W usłudze Azure IoT istnieją dwa sposoby uwierzytelniania certyfikatów:
- Odcisk palca. Algorytm odcisku palca jest uruchamiany na certyfikacie w celu wygenerowania ciągu szesnastkowego. Wygenerowany ciąg jest unikatowym identyfikatorem certyfikatu. 
- Uwierzytelnianie urzędu certyfikacji na podstawie pełnego łańcucha. Łańcuch certyfikatów to hierarchiczna lista wszystkich certyfikatów potrzebnych do uwierzytelnienia certyfikatu jednostki końcowej (EE). Aby uwierzytelnić certyfikat EE, konieczne jest uwierzytelnienie każdego certyfikatu w łańcuchu, w tym zaufanego głównego urzędu certyfikacji. 

Plusy dla X.509:
- X.509 jest najbezpieczniejszym typem uwierzytelniania obsługiwanym w usłudze Azure IoT.
- X.509 umożliwia wysoki poziom kontroli do celów zarządzania certyfikatami.
- Wielu dostawców jest dostępnych do dostarczania rozwiązań uwierzytelniania opartych na architekturze X.509.

Minusy dla X.509:
- Wielu klientów może być konieczne poleganie na zewnętrznych dostawców dla swoich certyfikatów.
- Zarządzanie certyfikatami może być kosztowne i zwiększa całkowity koszt rozwiązania.
- Zarządzanie cyklem życia certyfikatów może być trudne, jeśli logistyka nie jest dobrze przemyślana. 

### <a name="trusted-platform-module-tpm"></a>Moduł TPM
Moduł TPM, znany również jako [ISO/IEC 11889,](https://www.iso.org/standard/66510.html)jest standardem do bezpiecznego generowania i przechowywania kluczy kryptograficznych. Moduł TPM odnosi się również do wirtualnego lub fizycznego urządzenia We/Wy, które współdziała z modułami implementuuuującym standard. Urządzenie TPM może istnieć jako oddzielny sprzęt, zintegrowany sprzęt, moduł oparty na oprogramowaniu układowym lub moduł oparty na oprogramowaniu. 

Istnieją dwie kluczowe różnice między TPM i kluczy symetrycznych: 
- Układy TPM mogą również przechowywać certyfikaty X.509.
- Zaświadczanie tpm w dps używa klucza poręczenia modułu TPM (EK), forma uwierzytelniania asymetrycznego. W przypadku uwierzytelniania asymetrycznego klucz publiczny jest używany do szyfrowania, a oddzielny klucz prywatny jest używany do odszyfrowywania. Natomiast klucze symetryczne używają uwierzytelniania symetrycznego, gdzie klucz prywatny jest używany zarówno do szyfrowania, jak i odszyfrowywania. 

Plusy dla modułu TPM:
- TPM są dołączone jako standardowy sprzęt na wielu urządzeniach z systemem Windows, z wbudowaną obsługą systemu operacyjnego. 
- Zaświadczanie modułu TPM jest łatwiejsze do zabezpieczenia niż poświadczenie klucza symetrycznego opartego na symetrii symetrycznego symetrycznego symetrycznego symetrycznego symetrycznego symetrycznego sygnatury dostępu współdzielonego (SYGNATURA WSPÓŁDZIELONA).
- Można łatwo wygasnąć i odnowić lub przetoczyć poświadczenia urządzenia. Dps automatycznie rolkach poświadczeń Usługi IoT Hub, gdy urządzenie TPM jest ze względu na ponowne aprovisioning.

Wady dla TPM: 
- TPM są złożone i mogą być trudne w użyciu. 
- Tworzenie aplikacji z TPM jest trudne, chyba że masz fizyczny moduł TPM lub emulator jakości.
- Może być trzeba przeprojektować płytę urządzenia, aby uwzględnić moduł TPM w sprzęcie. 
- Jeśli rolki EK na moduł TPM, niszczy tożsamość modułu TPM i tworzy nowy. Chociaż układ fizyczny pozostaje taki sam, ma nową tożsamość w rozwiązaniu IoT.

### <a name="symmetric-key"></a>Klucz symetryczny
W przypadku kluczy symetrycznych ten sam klucz jest używany do szyfrowania i odszyfrowywania wiadomości. W rezultacie ten sam klucz jest znany zarówno urządzeniu, jak i usłudze, która go uwierzytelnia. Usługa Azure IoT obsługuje połączenia kluczy symetrycznych oparte na tokenach Symetrycznego Symetrycznego Symetrycznego Symetrycznego Symetrycznego. Uwierzytelnianie za klucz symetryczny wymaga znacznej odpowiedzialności właściciela, aby zabezpieczyć klucze i osiągnąć równy poziom zabezpieczeń za pomocą uwierzytelniania X.509. Jeśli używasz kluczy symetrycznych, zalecaną praktyką jest ochrona kluczy przy użyciu sprzętowego modułu zabezpieczeń (HSM).

Plusy dla klucza symetrycznego:
- Korzystanie z kluczy symetrycznych jest najprostszym, najniższym kosztem sposobem rozpoczęcia pracy z uwierzytelnianiem.
- Korzystanie z kluczy symetrycznych usprawnia proces, ponieważ nie ma nic dodatkowego do wygenerowania. 

Minusy dla klucza symetrycznego: 
- Klucze symetryczne podjąć znaczny wysiłek, aby zabezpieczyć klucze. Ten sam klucz jest współużytkowany przez urządzenie i chmurę, co oznacza, że klucz musi być chroniony w dwóch miejscach. Natomiast wyzwaniem z certyfikatami TPM i X.509 jest udowodnienie posiadania klucza publicznego bez ujawniania klucza prywatnego.
- Klucze symetryczne ułatwiają przestrzeganie złych praktyk w zakresie zabezpieczeń. Powszechną tendencją w przypadku kluczy symetrycznych jest zakodowanie niezaszyfrowanych kluczy na urządzeniach. Chociaż praktyka ta jest wygodna, pozostawia klucze narażone. Możesz ograniczyć pewne ryzyko, bezpiecznie przechowując klucz symetryczny na urządzeniu. Jeśli jednak priorytetem jest ostatecznie bezpieczeństwo, a nie wygoda, użyj certyfikatów X.509 lub modułu TPM do uwierzytelniania. 

### <a name="shared-symmetric-key"></a>Udostępniony klucz symetryczny
Istnieje odmiana uwierzytelniania klucza symetrycznego, znana jako udostępniony klucz symetryczny. Takie podejście polega na użyciu tego samego klucza symetrycznego we wszystkich urządzeniach. Zaleca się unikanie używania udostępnionych kluczy symetrycznych na urządzeniach. 

Pro dla udostępnionego klucza symetrycznego:
- Prosty do wdrożenia i niedrogi do produkcji na dużą skalę. 

Wady dla udostępnionego klucza symetrycznego: 
- Bardzo podatny na ataki. Ryzyko znacznie przeważa nad korzyściami płynącymi z łatwego wdrożenia. 
- Każdy może podszyć się pod Twoje urządzenia, jeśli uzyska klucz udostępniony.
- Jeśli korzystasz z udostępnionego klucza symetrycznego, który zostanie naruszony, prawdopodobnie utracisz kontrolę nad urządzeniami. 

### <a name="making-the-right-choice-for-your-devices"></a>Dokonywanie właściwego wyboru dla twoich urządzeń
Aby wybrać metodę uwierzytelniania, upewnij się, że bierzesz pod uwagę zalety i koszty każdego podejścia dla unikatowego procesu produkcyjnego.  W przypadku uwierzytelniania urządzeń zwykle istnieje odwrotna relacja między tym, jak bezpieczne jest dane podejście, a jego wygodą.  

## <a name="installing-certificates-on-iot-devices"></a>Instalowanie certyfikatów na urządzeniach IoT
Jeśli do uwierzytelniania urządzeń IoT używasz certyfikatów X.509, w tej sekcji znajdziesz wskazówki dotyczące integracji certyfikatów z procesem produkcyjnym. Musisz podjąć kilka decyzji.  Obejmują one decyzje dotyczące typowych zmiennych certyfikatów, kiedy wygenerować certyfikaty i kiedy je zainstalować. 

Jeśli używasz haseł, możesz zapytać, dlaczego nie możesz używać tego samego certyfikatu na wszystkich urządzeniach, w taki sam sposób, w jaki możesz używać tego samego hasła na wszystkich urządzeniach. Po pierwsze, używanie tego samego hasła wszędzie jest niebezpieczne. Praktyka naraziła firmy na poważne ataki DDoS, w tym ten, który zdjął DNS na wschodnim wybrzeżu USA kilka lat temu. Nigdy nie używaj tego samego hasła wszędzie, nawet w przypadku kont osobistych. Po drugie, certyfikat nie jest hasłem, ale unikatową tożsamością. Hasło jest jak tajny kod, którego każdy może użyć do otwarcia drzwi w zabezpieczonym budynku.  To coś, co wiesz, i można dać hasło każdemu, aby uzyskać wejście.  Zaświadczenie jest jak prawo jazdy ze zdjęciem i innymi szczegółami, które możesz pokazać strażnikowi, aby dostać się do zabezpieczonego budynku. Jest związany z tym, kim jesteś.  Pod warunkiem, że strażnik dokładnie dopasowuje ludzi z prawem jazdy, tylko ty możesz użyć swojego prawa jazdy (tożsamości), aby uzyskać wejście. 

### <a name="variables-involved-in-certificate-decisions"></a>Zmienne związane z decyzjami o świadectwie
Należy wziąć pod uwagę następujące zmienne i jak każdy z nich wpływa na cały proces produkcyjny. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>W przypadku gdy katalog główny certyfikatu zaufania pochodzi z
Zarządzanie infrastrukturą kluczy publicznych (PKI) może być kosztowne i skomplikowane.  Zwłaszcza jeśli Twoja firma nie ma żadnego doświadczenia w zarządzaniu pki. Dostępne możliwości to:
- Użyj infrastruktury kluczy publicznych innych firm. Certyfikaty podpisywania pośredniego można kupić od dostawcy certyfikatów innej firmy. Można też użyć prywatnego urzędu certyfikacji(CA). 
- Użyj zarządzanej przez siebie infrastruktury kluczy publicznych. Można utrzymywać własny system infrastruktury kluczy publicznych i generować własne certyfikaty.
- Użyj usługi zabezpieczeń [usługi Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Ta opcja dotyczy tylko urządzeń usługi Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>W przypadku przechowywania certyfikatów
Istnieje kilka czynników, które mają wpływ na decyzję o miejscu przechowywania certyfikatów. Czynniki te obejmują typ urządzenia, oczekiwane marże zysku (niezależnie od tego, czy możesz sobie pozwolić na bezpieczną pamięć masową), możliwości urządzenia i istniejącą technologię zabezpieczeń na urządzeniu, z którego można korzystać. Rozważmy następujące opcje:
- W sprzętowym module zabezpieczeń (HSM). Zaleca się korzystanie z modułu HSM. Sprawdź, czy na płycie sterującej urządzenia jest już zainstalowany moduł HSM. Jeśli wiesz, że nie masz modułu HSM, skontaktuj się z producentem sprzętu, aby zidentyfikować moduł HSM spełniający Twoje potrzeby.
- W bezpiecznym miejscu na dysku, takim jak zaufane środowisko wykonywania (TEE).
- W lokalnym systemie plików lub magazynie certyfikatów. Na przykład magazyn certyfikatów systemu Windows. 

#### <a name="connectivity-at-the-factory"></a>Łączność w fabryce
Łączność w fabryce określa, jak i kiedy otrzymasz certyfikaty do zainstalowania na urządzeniach. Opcje łączności są następujące:
- Łączność. Łączność jest optymalna, usprawnia proces generowania certyfikatów lokalnie. 
- Brak łączności. W takim przypadku do generowania certyfikatów urządzeń lokalnie i w trybie offline używany jest podpisany certyfikat urzędu certyfikacji. 
- Brak łączności. W takim przypadku można uzyskać certyfikaty, które zostały wygenerowane z wyprzedzeniem. Możesz też użyć infrastruktury kluczy publicznych w trybie offline do lokalnego generowania certyfikatów.

#### <a name="audit-requirement"></a>Wymóg audytu
W zależności od typu wytwarzanych urządzeń może być wymagane przez przepisy, aby utworzyć ścieżkę inspekcji sposobu instalowania tożsamości urządzeń na urządzeniach. Inspekcja zwiększa koszty produkcji. Tak więc w większości przypadków, tylko zrobić to w razie potrzeby. Jeśli nie masz pewności, czy audyt jest wymagany, skontaktuj się z działem prawnym firmy. Opcje inspekcji to: 
- Nie jest to branża wrażliwa. Inspekcja nie jest wymagana.
- Branża wrażliwa. Certyfikaty powinny być instalowane w bezpiecznym pomieszczeniu zgodnie z wymogami certyfikacji zgodności. Jeśli potrzebujesz bezpiecznego pomieszczenia do instalowania certyfikatów, prawdopodobnie wiesz już, w jaki sposób certyfikaty są instalowane na urządzeniach. I prawdopodobnie masz już system audytu. 

#### <a name="length-of-certificate-validity"></a>Długość ważności certyfikatu
Podobnie jak prawo jazdy, certyfikaty mają datę wygaśnięcia, która jest ustawiona podczas ich tworzenia. Oto opcje dotyczące długości ważności certyfikatu:
- Odnowienie nie jest wymagane.  Takie podejście używa długiego okresu odnawiania, więc nigdy nie trzeba odnawiać certyfikatu w okresie istnienia urządzenia. Chociaż takie podejście jest wygodne, jest również ryzykowne.  Możesz zmniejszyć ryzyko, używając bezpiecznego magazynu, takiego jak moduł HSM na urządzeniach. Jednak zalecaną praktyką jest unikanie używania certyfikatów długotrwałych.
- Wymagane odnowienie.  Musisz odnowić certyfikat w okresie istnienia urządzenia. Długość ważności certyfikatu zależy od kontekstu i będziesz potrzebować strategii odnawiania.  Strategia powinna obejmować, gdzie otrzymujesz certyfikaty i jakiego typu funkcje over-the-air urządzenia muszą używać w procesie odnawiania. 

### <a name="when-to-generate-certificates"></a>Kiedy wygenerować certyfikaty
Możliwości łączności internetowej w fabryce będą miały wpływ na proces generowania certyfikatów. Istnieje kilka opcji, kiedy do generowania certyfikatów: 

- Wstępnie załadowane certyfikaty.  Niektórzy dostawcy modułu HSM oferują usługę premium, w której dostawca modułu HSM instaluje certyfikaty dla odbiorcy. Po pierwsze, klienci dają dostawcy HSM dostęp do certyfikatu podpisywania. Następnie dostawca modułu HSM instaluje certyfikaty podpisane przez ten certyfikat podpisywania na każdym pliku HSM, który klient kupuje. Wszystko, co klient musi zrobić, to zainstalować moduł HSM na urządzeniu. Usługa ta zwiększa koszty, ale pomaga usprawnić proces produkcyjny.  I rozwiązuje pytanie, kiedy zainstalować certyfikaty.
- Certyfikaty generowane przez urządzenie.  Jeśli urządzenia generują certyfikaty wewnętrznie, należy wyodrębnić publiczny certyfikat X.509 z urządzenia, aby zarejestrować go w dps. 
- Połączona fabryka.  Jeśli fabryka ma łączność, można wygenerować certyfikaty urządzeń, gdy ich potrzebujesz.
- Fabryka offline z własną pki. Jeśli fabryka nie ma łączności i używasz własnej infrastruktury kluczy publicznych z obsługą w trybie offline, możesz wygenerować certyfikaty, gdy są potrzebne.
- Fabryka w trybie offline z pomocą infrastruktury kluczy publicznych innych firm. Jeśli fabryka nie ma łączności i używasz infrastruktury kluczy publicznych innych firm, należy wygenerować certyfikaty z wyprzedzeniem. I konieczne będzie wygenerowanie certyfikatów z lokalizacji, która ma łączność. 

### <a name="when-to-install-certificates"></a>Kiedy zainstalować certyfikaty
Po wygenerowaniu certyfikatów dla urządzeń IoT można je zainstalować w urządzeniach. 

Jeśli używasz wstępnie załadowanych certyfikatów z modułem HSM, proces ten jest uproszczony. Po zainstalowaniu modułu HSM w urządzeniu kod urządzenia może uzyskać do niego dostęp. Następnie wywołasz interfejsy API hsm, aby uzyskać dostęp do certyfikatu, który jest przechowywany w modułie HSM. Takie podejście jest najwygodniejsze dla procesu produkcyjnego. 

Jeśli nie używasz wstępnie załadowanego certyfikatu, należy zainstalować certyfikat jako część procesu produkcyjnego. Najprostszym rozwiązaniem jest zainstalowanie certyfikatu w modułie HSM w tym samym czasie, w tym co miga początkowy obraz oprogramowania układowego. Proces musi dodać krok, aby zainstalować obraz na każdym urządzeniu. Po tym kroku można uruchomić końcowe kontrole jakości i inne kroki, zanim zapakujesz i wyślesz urządzenie. 

Dostępne są narzędzia programowe, które umożliwiają uruchomienie procesu instalacji i ostateczne sprawdzenie jakości w jednym kroku. Można zmodyfikować te narzędzia, aby wygenerować certyfikat lub wyciągnąć certyfikat z wstępnie wygenerowanego magazynu certyfikatów. Następnie oprogramowanie może zainstalować certyfikat, w którym trzeba go zainstalować. Narzędzia programowe tego typu umożliwiają uruchamianie produkcji na dużą skalę. 

Po zainstalowaniu certyfikatów na urządzeniach następnym krokiem jest dowiedzenie się, jak zarejestrować urządzenia za pomocą [dps](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integracja modułu TPM z procesem produkcyjnym
Jeśli używasz modułu TPM do uwierzytelniania urządzeń IoT, ta sekcja zawiera wskazówki. Wskazówki obejmują powszechnie używane urządzenia TPM 2.0, które mają obsługę klucza kodu uwierzytelniania wiadomości opartego na skrótach (HMAC). Specyfikacja modułu TPM dla układów TPM jest standardem ISO utrzymywanym przez Trusted Computing Group. Więcej informacji na temat modułu TPM można znaleźć w specyfikacjach [modułów TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) i [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Przejęcie na własność modułu TPM
Kluczowym krokiem w produkcji urządzenia z chipem TPM jest przejęcie na własność modułu TPM. Ten krok jest wymagany, aby można było podać klucz do właściciela urządzenia. Pierwszym krokiem jest wyodrębnienie klucza poręczenia (EK) z urządzenia. Następnym krokiem jest rzeczywiście roszczenia własności. Sposób osiągnięcia tego celu zależy od tego, którego modułu TPM i systemu operacyjnego używasz. W razie potrzeby skontaktuj się z producentem modułu TPM lub deweloperem systemu operacyjnego urządzenia, aby ustalić, jak zgłosić prawo własności. 

W procesie produkcyjnym można wyodrębnić EK i ubiegać się o własność w różnym czasie, co zwiększa elastyczność. Wielu producentów korzysta z tej elastyczności, dodając sprzętowy moduł zabezpieczeń (HSM), aby zwiększyć bezpieczeństwo swoich urządzeń. W tej sekcji przedstawiono wskazówki dotyczące tego, kiedy wyodrębnić ek, kiedy do przejęcia własności modułu TPM i zagadnienia dotyczące integracji tych kroków w harmonogramie produkcji. 

> [!IMPORTANT]
> W poniższych wskazówkach przyjęto założenie, że używany jest dyskretny, firmware lub zintegrowany moduł TPM. W miejscach, w których ma to zastosowanie, wskazówki dodają uwagi dotyczące korzystania z modułu TPM niedyskretnego lub programowego. Jeśli używasz modułu TPM oprogramowania, mogą istnieć dodatkowe kroki, których ta wskazówka nie zawiera. Programy TPM oprogramowania mają wiele implementacji, które wykraczają poza zakres tego artykułu.  Ogólnie rzecz biorąc, możliwe jest zintegrowanie modułu TPM oprogramowania z następującą ogólną osią czasu produkcji. Jednak podczas gdy emulowane oprogramowanie moduł TPM nadaje się do prototypowania i testowania, nie może zapewnić takiego samego poziomu zabezpieczeń jak dyskretny, firmware lub zintegrowany moduł TPM. W praktyce należy unikać używania modułu TPM oprogramowania w produkcji.

### <a name="general-manufacturing-timeline"></a>Ogólny harmonogram produkcji
Na poniższej osi czasu pokazano, jak moduł TPM przechodzi przez proces produkcyjny i kończy się w urządzeniu. Każdy proces produkcyjny jest unikatowy, a ta oś czasu pokazuje najczęstsze wzorce. Oś czasu zawiera wskazówki dotyczące tego, kiedy należy podjąć pewne działania za pomocą kluczy. 

#### <a name="step-1-tpm-is-manufactured"></a>Krok 1: TPM jest produkowany
- Jeśli kupujesz tpm do użytku w urządzeniach, sprawdź, czy będą one wyodrębnić publiczne klucze poparcia (EK_pubs) dla Ciebie. Jest to przydatne, jeśli producent udostępnia listę EK_pubs z dostarczonymi urządzeniami. 
    > [!NOTE]
    > Można udzielić producentowi modułu TPM dostępu do zapisu na liście rejestracji przy użyciu zasad dostępu współużytkującego w usłudze inicjowania obsługi administracyjnej.  Takie podejście pozwala im dodać TPM do listy rejestracji dla Ciebie.  Ale to jest na wczesnym etapie procesu produkcyjnego i wymaga zaufania do producenta modułu TPM. Zrób to na własne ryzyko. 

- Jeśli produkujesz TPM do sprzedaży producentom urządzeń, rozważ nadanie klientom listy EK_pubs wraz z ich fizycznymi tpmami.  Zapewnienie klientom EK_pubs pozwala zaoszczędzić krok w ich procesie. 
- Jeśli produkujesz TPM do użycia z własnymi urządzeniami, określ, który punkt w procesie jest najwygodniejszy do wyodrębnienia EK_pub. EK_pub można wyodrębnić w dowolnym z pozostałych punktów na osi czasu. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Krok 2: Moduł TPM jest zainstalowany w urządzeniu
W tym momencie procesu produkcyjnego należy wiedzieć, które wystąpienie DPS urządzenie będzie używane z. W rezultacie można dodać urządzenia do listy rejestracji do automatycznego inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji na temat automatycznego inicjowania obsługi administracyjnej urządzeń, zobacz [dokumentację DPS](about-iot-dps.md).
- Jeśli nie wyodrębniłeś EK_pub, teraz jest dobry moment, aby to zrobić. 
- W zależności od procesu instalacji modułu TPM ten krok jest również dobry czas, aby przejąć na własność modułu TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Krok 3: Urządzenie ma zainstalowane oprogramowanie układowe i oprogramowanie
W tym momencie procesu zainstaluj klienta DPS wraz z zakresem identyfikatora i globalnym adresem URL do inicjowania obsługi administracyjnej.
- Teraz jest ostatnia szansa, aby wyodrębnić EK_pub. Jeśli osoba trzecia zainstaluje oprogramowanie na urządzeniu, warto najpierw wyodrębnić EK_pub.
- Ten punkt w procesie produkcyjnym jest idealny do przejęcia na własność modułu TPM.  
    > [!NOTE]
    > Jeśli używasz modułu TPM oprogramowania, możesz go teraz zainstalować.  Wyodrębnić EK_pub w tym samym czasie.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Krok 4: Urządzenie jest pakowane i wysyłane do magazynu
Urządzenie może siedzieć w magazynie przez 6-12 miesięcy przed wdrożeniem. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Krok 5: Urządzenie jest zainstalowane w lokalizacji
Po dotarciu urządzenia do ostatecznej lokalizacji, przechodzi przez automatyczne inicjowanie obsługi administracyjnej za pomocą dps.

Aby uzyskać więcej informacji, zobacz [Pojęcia dotyczące automatycznej obsługi administracyjnej](concepts-auto-provisioning.md) i [zaświadczanie modułu TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Resources

Oprócz zalecanych rozwiązań w zakresie zabezpieczeń w tym artykule usługa Azure IoT udostępnia zasoby ułatwiające wybieranie bezpiecznego sprzętu i tworzenie bezpiecznych wdrożeń IoT: 
- Zalecenia dotyczące [zabezpieczeń](../iot-fundamentals/security-recommendations.md) usługi Azure IoT, aby poprowadzić proces wdrażania. 
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) oferuje usługę ułatwiające tworzenie bezpiecznych wdrożeń IoT. 
- Aby uzyskać pomoc dotyczącą oceny środowiska sprzętowego, zobacz oficjalny dokument [Oceniający zabezpieczenia IoT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Aby uzyskać pomoc dotyczącą wybierania bezpiecznego sprzętu, zobacz [Odpowiedni bezpieczny sprzęt do wdrożenia IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 