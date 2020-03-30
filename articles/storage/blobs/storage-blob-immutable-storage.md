---
title: Niezmienny magazyn obiektów blob — usługa Azure Storage
description: Usługa Azure Storage oferuje obsługę WORM (Write Once, Read Many) dla magazynu obiektów blob, która umożliwia użytkownikom przechowywanie danych w stanie nieekserwowania, niemodyfikowalne dla określonego interwału.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367622"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Przechowywanie danych obiektów blob o znaczeniu krytycznym dla firmy z niezmiennym magazynem

Niezmienne magazynu dla magazynu obiektów Blob platformy Azure umożliwia użytkownikom do przechowywania obiektów danych o znaczeniu krytycznym dla firmy w WORM (Zapisz raz, Przeczytaj wiele) stan. Ten stan sprawia, że dane nie można wymazać i nie można modyfikować dla interwału określonego przez użytkownika. Przez cały czas trwania interwału przechowywania obiekty blob mogą być tworzone i odczytywane, ale nie można ich modyfikować ani usuwać. Niezmienne magazynu jest dostępny dla ogólnego przeznaczenia v1, ogólnego przeznaczenia v2, BlobStorage i BlockBlobStorage kont we wszystkich regionach platformy Azure.

Aby uzyskać informacje dotyczące ustawiania i wyczyszczania blokad prawnych lub tworzenia zasad przechowywania opartych na czasie przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure, zobacz [Ustawianie zasad niezmienności i zarządzanie nimi dla magazynu obiektów Blob.](storage-blob-immutability-policies-manage.md)

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Informacje o niezmiennym magazynie obiektów blob

Niezmienne przechowywanie pomaga organizacji opieki zdrowotnej,&mdash;instytucjom finansowym i&mdash;powiązanym branżom, szczególnie organizacjom brokerów i dealerów w bezpiecznym przechowywaniu danych. Niezmienne miejsce do magazynowania można również wykorzystać w dowolnym scenariuszu, aby chronić krytyczne dane przed modyfikacją lub usunięciem.

Typowe zastosowania tej funkcji to:

- **Zgodność z przepisami:** Niezmienne magazynowanie dla magazynu obiektów Blob platformy Azure pomaga organizacjom zająć się sec 17a-4(f), CFTC 1.31(d), FINRA i innymi przepisami. Techniczny oficjalny dokument firmy Cohasset Associates szczegółowo opisuje, w jaki sposób niezmienne przechowywanie spełnia te wymagania prawne można pobrać za pośrednictwem [portalu Microsoft Service Trust Portal.](https://aka.ms/AzureWormStorage) [Centrum zaufania platformy Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) zawiera szczegółowe informacje na temat naszych certyfikatów zgodności.

- **Bezpieczne przechowywanie dokumentów:** Niezmienne miejsce do magazynowania obiektów Blob zapewnia, że dane nie mogą być modyfikowane ani usuwane przez dowolnego użytkownika, w tym użytkowników z uprawnieniami administracyjnymi konta.

- **Blokada prawna:** Niezmienne magazynu dla usługi Azure Blob storage umożliwia użytkownikom do przechowywania poufnych informacji, które są krytyczne dla postępowania sądowego lub użytku biznesowego w stanie odporne na manipulacje przez żądany czas trwania, aż do usunięcia blokady. Ta funkcja nie ogranicza się tylko do przypadków użycia prawnego, ale może być również traktowane jako blokada oparta na zdarzeniach lub blokada przedsiębiorstwa, gdzie wymagana jest potrzeba ochrony danych na podstawie wyzwalaczy zdarzeń lub zasad firmowych.

Niezmienne przechowywanie obsługuje następujące funkcje:

- **[Obsługa zasad przechowywania na podstawie czasu:](#time-based-retention-policies)** Użytkownicy mogą ustawiać zasady do przechowywania danych dla określonego interwału. Po ustawieniu zasad przechowywania opartych na czasie można tworzyć i odczytywać obiekty BLOB, ale nie są modyfikowane ani usuwane. Po upływie okresu przechowywania obiekty BLOB można usunąć, ale nie zostały zastąpione.

- **[Obsługa zasad blokady prawnej:](#legal-holds)** Jeśli interwał przechowywania nie jest znany, użytkownicy mogą ustawić blokady prawne do przechowywania niezmiennych danych, dopóki blokada prawna nie zostanie wyczyszczona.  Po ustawieniu zasad wstrzymania prawnego można tworzyć i odczytywać obiekty BLOB, ale nie są modyfikowane ani usuwane. Każdy blokada prawna jest skojarzona z tagiem alfanumerycznym zdefiniowanym przez użytkownika (takim jak identyfikator sprawy, nazwa zdarzenia itp.), który jest używany jako ciąg identyfikatora. 

- **Obsługa wszystkich warstw obiektów blob:** zasady WORM są niezależne od warstwy magazynu obiektów Blob platformy Azure i mają zastosowanie do wszystkich warstw: gorąca, chłodna i archiwum. Użytkownicy mogą przesyłać dane do warstwy najbardziej zoptymalizowanej pod kątem ich obciążeń, jednocześnie gwarantując ich niezmienność.

- **Konfiguracja na poziomie kontenera:** Użytkownicy mogą konfigurować zasady przechowywania oparte na czasie i prawne tagi wstrzymania na poziomie kontenera. Dzięki korzystaniu z prostych ustawień na poziomie kontenera użytkownicy mogą tworzyć i blokować zasady przechowywania na podstawie czasu, wydłużać okresy przechowywania, ustawiać i usuwać stan archiwizacji ze względów prawnych itp. Te zasady są stosowane do wszystkich obiektów blob w kontenerze — zarówno istniejących, jak i nowych.

- **Obsługa rejestrowania inspekcji:** Każdy kontener zawiera dziennik inspekcji zasad. Pokazuje do siedmiu opartych na czasie poleceń przechowywania dla zablokowanych zasad przechowywania opartych na czasie i zawiera identyfikator użytkownika, typ polecenia, sygnatury czasowe i interwał przechowywania. W przypadku archiwizacji ze względów prawnych dziennik zawiera identyfikator użytkownika, typ polecenia, znaczniki czasu oraz tagi archiwizacji ze względów prawnych. Ten dziennik jest zachowywany przez cały okres istnienia polityki, zgodnie z wytycznymi regulacyjnymi SEC 17a-4(f). [Dziennik aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) zawiera bardziej kompleksowy dziennik wszystkich działań płaszczyzny sterowania; podczas włączania [dzienników diagnostycznych platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) zachowuje i pokazuje operacje płaszczyzny danych. Przechowywanie tych dzienników w sposób trwały, zgodnie z obowiązującymi wymogami prawnymi lub innymi, należy do obowiązków użytkownika.

## <a name="how-it-works"></a>Jak to działa

Funkcja magazynu niezmiennego w usłudze Azure Blob Storage obsługuje dwa rodzaje zasad WORM (nazywanych też zasadami magazynu niezmiennego): zasady przechowywania na podstawie czasu i zasady archiwizacji ze względów prawnych. Gdy zasady przechowywania na podstawie czasu lub blokady prawnej jest stosowany w kontenerze, wszystkie istniejące obiekty blob przenieść do niezmiennego stanu WORM w mniej niż 30 sekund. Wszystkie nowe obiekty BLOB, które są przekazywane do tego kontenera chronionego zasad będzie również przenieść do stanu niezmienne. Gdy wszystkie obiekty BLOB są w stanie niezmiennym, zasady niezmienne jest potwierdzona i wszelkie operacje zastępowania lub usuwania w kontenerze niezmienne są niedozwolone.

Usunięcie konta kontenera i magazynu nie są również dozwolone, jeśli w kontenerze znajdują się obiekty blob, które są chronione przez blokadę prawną lub zablokowane zasady oparte na czasie. Zasady legalnego przechowywania będą chronić przed usunięciem obiektów blob, kontenerów i kont magazynu. Zarówno odblokowane, jak i zablokowane zasady oparte na czasie będą chronić przed usunięciem obiektu blob przez określony czas. Odblokowane i zablokowane zasady oparte na czasie będą chronić przed usunięciem kontenera tylko wtedy, gdy w kontenerze istnieje co najmniej jeden obiekt blob. Tylko kontener z *zablokowanymi* zasadami opartymi na czasie będzie chronić przed usunięciem konta magazynu; kontenery z odblokowanymi zasadami czasowymi nie oferują ochrony przed usunięciem konta magazynu ani zgodności.

Aby uzyskać więcej informacji na temat ustawiania i blokowania zasad przechowywania opartych na czasie, zobacz [Ustawianie zasad niezmienności i zarządzanie nimi dla magazynu obiektów Blob](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Oparte na czasie zasady przechowywania

> [!IMPORTANT]
> Zasady przechowywania oparte na czasie muszą być *zablokowane,* aby obiekt blob był w stanie niezmiennym (ochrona zapisu i usuwania) dla sec 17a-4(f) i innych zgodności z przepisami. Zaleca się zablokowanie zasad w rozsądnym czasie, zazwyczaj krótszym niż 24 godziny. Początkowy stan zastosowanej zasady przechowywania opartej na czasie jest *odblokowany,* co pozwala przetestować tę funkcję i wprowadzić zmiany w zasadach przed jej zablokowaniem. Podczas gdy stan *odblokowany* zapewnia ochronę niezmienności, nie zaleca się używania stanu *odblokowania* do celów innych niż krótkoterminowe próby funkcji. 

Gdy zasady przechowywania oparte na czasie są stosowane w kontenerze, wszystkie obiekty blob w kontenerze pozostaną w stanie niezmiennym przez czas trwania *okresu przechowywania.* Okres efektywnego przechowywania obiektów blob jest równy różnicy między **czasem tworzenia** obiektu blob a interwałem przechowywania określonym przez użytkownika. Ponieważ użytkownicy mogą rozszerzać okres przechowywania, na potrzeby obliczania obowiązującego okresu przechowywania magazyn niezmienny korzysta z ostatniej wartości okresu przechowywania ustawionej przez użytkownika.

Załóżmy na przykład, że użytkownik tworzy zasady przechowywania oparte na czasie z interwałem przechowywania wynoszącym pięć lat. Istniejący obiekt blob w tym kontenerze, _testblob1_, został utworzony rok temu; tak, efektywny okres retencji dla _testblob1_ wynosi cztery lata. Gdy nowy obiekt blob, _testblob2_, jest przekazyany do kontenera, okres efektywnego przechowywania dla _testblob2_ wynosi pięć lat od momentu jego utworzenia.

Zasady przechowywania odblokowane w czasie są zalecane tylko do testowania funkcji, a zasady muszą być zablokowane, aby były zgodne z sec 17a-4(f) i innymi zgodnościami z przepisami. Po zablokowaniu zasad przechowywania opartych na czasie nie można usunąć zasad i maksymalnie pięć podwyżek do efektywnego okresu przechowywania.

Do zasad przechowywania mają zastosowanie następujące limity:

- W przypadku konta magazynu maksymalna liczba kontenerów z zablokowanymi zasadami niezmiennymi opartymi na czasie wynosi 10 000.
- Minimalny interwał przechowywania wynosi 1 dzień. Maksymalna wartość to 146 000 dni (400 lat).
- W przypadku kontenera maksymalna liczba zmian w celu wydłużenia interwału przechowywania dla zablokowanych zasad niezmiennych opartych na czasie wynosi 5.
- W przypadku kontenera dla kontenera nie tylko siedem opartych na czasie dzienników inspekcji zasad przechowywania są zachowywane dla zablokowanych zasad.

### <a name="allow-protected-append-blobs-writes"></a>Zezwalaj na zapisy chronionych obiektów blob

Dołączanie obiektów blob składają się z bloków danych i zoptymalizowane pod kątem operacji dołączania danych wymaganych przez inspekcję i rejestrowanie scenariuszy. Zgodnie z projektem dołączanie obiektów blob zezwalają tylko na dodawanie nowych bloków na końcu obiektu blob. Niezależnie od niezmienności, modyfikacji lub usunięcia istniejących bloków w dołączyć obiektu blob jest zasadniczo niedozwolone. Aby dowiedzieć się więcej o dołączaniu obiektów blob, zobacz [Informacje o dołączaniu obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Tylko oparte na czasie `allowProtectedAppendWrites` zasady przechowywania mają ustawienie, które umożliwia zapisywanie nowych bloków do dołączania obiektu blob przy zachowaniu ochrony niezmienności i zgodności. Jeśli ta opcja jest włączona, można utworzyć obiekt blob dołączany bezpośrednio w kontenerze chronionym zasadami i kontynuować dodawanie nowych bloków danych na końcu istniejących dodatkowych obiektów blob przy użyciu interfejsu API *AppendBlock.* Można dodawać tylko nowe bloki i nie można modyfikować ani usuwać istniejących bloków. Ochrona niezmienność przechowywania czasu nadal ma zastosowanie, zapobiegając usunięciu dołączanego obiektu blob do czasu upłynie okres efektywnego przechowywania. Włączenie tego ustawienia nie wpływa na zachowanie niezmienności bloków obiektów blob lub stronicowych obiektów blob.

Ponieważ to ustawienie jest częścią zasad przechowywania opartych na czasie, dołączanie obiektów blob nadal pozostają w stanie niezmienne przez czas trwania *okresu przechowywania efektywnego.* Ponieważ nowe dane mogą być dołączane poza początkowe tworzenie dołączania obiektu blob, istnieje niewielka różnica w określaniu okresu przechowywania. Efektywne przechowywanie jest różnica między **czas ostatniej modyfikacji** dołączania obiektu blob i interwał przechowywania określony przez użytkownika. Podobnie, gdy interwał przechowywania jest przedłużony, niezmienne magazyn używa najnowszej wartości interwału przechowywania określonego przez użytkownika do obliczania efektywnego okresu przechowywania.

Załóżmy na przykład, że użytkownik tworzy `allowProtectedAppendWrites` zasady przechowywania oparte na czasie z włączonym i interwał przechowywania 90 dni. Dołącz obiekt blob, _logblob1_, jest tworzony w kontenerze dzisiaj, nowe dzienniki nadal są dodawane do dołączania obiektu blob przez następne 10 dni; tak, efektywny okres przechowywania _logblob1_ jest 100 dni od dzisiaj (czas jego ostatniego dodatku + 90 dni).

Odblokowane zasady przechowywania oparte `allowProtectedAppendWrites` na czasie umożliwiają włączenie i wyłączenie tego ustawienia w dowolnym momencie. Po zablokowaniu zasad przechowywania opartych `allowProtectedAppendWrites` na czasie nie można zmienić tego ustawienia.

Zasady wstrzymania `allowProtectedAppendWrites` prawnego nie mogą być włączane, a wszelkie blokady prawne zniweczą właściwość "allowProtecAppendWrites". Jeśli blokada prawna jest stosowana do zasad `allowProtectedAppendWrites` przechowywania opartych na czasie z włączoną, interfejs API *dodatkuBlock* zakończy się niepowodzeniem, dopóki blokada prawna nie zostanie zniesiona.

## <a name="legal-holds"></a>Archiwizacja ze względów prawnych

Blokady prawne są blokadami tymczasowymi, które mogą być wykorzystywane do celów dochodzenia prawnego lub ogólnych zasad ochrony. Każda polityka prawna musi być skojarzona z co najmniej jednym tagiem. Znaczniki są używane jako nazwany identyfikator, taki jak identyfikator sprawy lub zdarzenie, do kategoryzowanie i opisywanie celu wstrzymania.

Kontener może mieć zarówno blokady prawnej i zasady przechowywania oparte na czasie w tym samym czasie. Wszystkie obiekty blob w tym kontenerze pozostają w stanie niezmiennym do momentu usunięcia stanu archiwizacji ze względów prawnych nawet po upływie obowiązującego okresu przechowywania. I odwrotnie — obiekty blob pozostają w stanie niezmiennym przez cały obowiązujący okres przechowywania nawet po usunięciu stanu archiwizacji ze względów prawnych.

Następujące limity mają zastosowanie do blokad prawnych:

- W przypadku konta magazynu maksymalna liczba kontenerów z ustawieniem blokady prawnej wynosi 10 000.
- W przypadku kontenera maksymalna liczba tagów blokady prawnej wynosi 10.
- Minimalna długość tagu blokady prawnej to trzy znaki alfanumeryczne. Maksymalna długość wynosi 23 znaki alfanumeryczne.
- W przypadku kontenera maksymalnie 10 dzienników inspekcji zasad blokady prawnej jest zachowywanych przez czas trwania zasad.

## <a name="scenarios"></a>Scenariusze
W poniższej tabeli przedstawiono typy operacji magazynu obiektów Blob, które są wyłączone dla różnych scenariuszy niezmienne. Aby uzyskać więcej informacji, zobacz dokumentację [interfejsu API interfejsu SPOCZYN USŁUGI Azure Blob.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

|Scenariusz  |Stan obiektu blob  |Odmowa operacji obiektów blob  |Ochrona kontenerów i kont
|---------|---------|---------|---------|
|Trwa obowiązujący okres przechowywania obiektu blob i/lub ustawiono stan archiwizacji ze względów prawnych     |Niezmienny: ochrona przed usuwaniem i zapisem         | Umieść obiekt Blob<sup>1</sup>, Umieść blok<sup>1</sup>, Umieść listę bloków<sup>1</sup>, Usuń kontener, Usuń obiekt blob, Ustaw metadane obiektów blob, Umieść stronę, Ustaw właściwości obiektu Blob, Obiekt Blob migawki, Przyrostowy blok blob, Dołącz blok<sup>2</sup>         |Odmowa usunięcia kontenera; Odmowa usunięcia konta magazynu         |
|Efektywny interwał retencji na obiekcie blob wygasł i nie ustawiono blokady prawnej    |Ochrona tylko przed zapisem (operacje usuwania są dozwolone)         |Umieść obiekt Blob<sup>1</sup>, Umieść blok<sup>1</sup>, Umieść listę bloków<sup>1</sup>, Ustaw metadane obiektów Blob, Umieść stronę, Ustaw właściwości obiektu Blob, Obiekt blob migawki, Blok kopii przyrostowej, Dołącz blok<sup>2</sup>         |Odmowa usunięcia kontenera, jeśli w chronionym kontenerze istnieje co najmniej 1 obiekt blob; Odmowa usunięcia konta magazynu tylko w przypadku *zablokowanych* zasad opartych na czasie         |
|Nie stosuje się zasad WORM (bez przechowywania opartego na czasie i bez prawnego znacznika wstrzymania)     |Modyfikowalny         |Brak         |Brak         |

<sup>1</sup> Usługa obiektów blob umożliwia tym operacjom utworzenie nowego obiektu blob raz. Wszystkie kolejne operacje zastępowania istniejącej ścieżki obiektu blob w kontenerze niezmiennym nie są dozwolone.

<sup>2</sup> Dołącz blok jest dozwolone tylko dla zasad `allowProtectedAppendWrites` przechowywania na podstawie czasu z włączoną właściwością. Aby uzyskać więcej informacji, zobacz [Zezwalaj na chronione dołączanie zapisów obiektów blob.](#allow-protected-append-blobs-writes)

## <a name="pricing"></a>Cennik

Korzystanie z tej funkcji nie wiąże się z dodatkową opłatą. Niezmienne dane są wyceniane w taki sam sposób jak dane modyfikowalne. Szczegółowe informacje o cenach w magazynie obiektów Blob platformy Azure można znaleźć na [stronie cennika usługi Azure Storage.](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="faq"></a>Najczęściej zadawane pytania

**Czy można dostarczyć dokumentację zgodności WORM?**

Tak. Aby udokumentować zgodność, firma Microsoft zachowała wiodącą niezależną firmę oceniającą, która specjalizuje się w zarządzaniu rekordami i informacjami, Cohasset Associates, w celu oceny niezmiennego przechowywania obiektów Blob i jego zgodności z wymaganiami specyficznymi dla sektora usług finansowych. Cohasset potwierdził, że niezmienne gołym okiem magazyn obiektów Blob, gdy jest używany do przechowywania obiektów blob opartych na czasie w stanie WORM, spełnia odpowiednie wymagania dotyczące magazynowania, których jest reguła 1.31(c)-(d), reguła 4511 i reguła SEC 17a-4. Firma Microsoft skierowała ten zestaw reguł, ponieważ stanowią one najbardziej nakazowe wskazówki na całym świecie dotyczące przechowywania rekordów dla instytucji finansowych. Raport Cohasset jest dostępny w [Centrum zaufania usług firmy Microsoft](https://aka.ms/AzureWormStorage). Aby poprosić firmę Microsoft o zaświadczenie dotyczące zgodności z programem WORM, skontaktuj się z pomocą techniczną platformy Azure.

**Czy funkcja dotyczy tylko blokować obiektów blob i dołączać obiekty blob lub do stronicowych obiektów blob, jak również?**

Niezmienne magazynu można używać z dowolnego typu obiektu blob, jak jest ustawiony na poziomie kontenera, ale zaleca się użycie WORM dla kontenerów, które głównie przechowywania bloków obiektów blob i dołączanie obiektów blob. Istniejące obiekty BLOB w kontenerze będą chronione przez nowo ustawioną zasadę WORM. Ale wszystkie nowe obiekty blob strony muszą być tworzone poza kontenerem WORM, a następnie kopiowane w. Po skopiowaniu do kontenera WORM nie są dozwolone żadne dalsze zmiany w stronicowym obiekcie blob. Ustawienie zasad WORM w kontenerze, który przechowuje dyski VHD (stronicowe obiekty blob) dla wszystkich aktywnych maszyn wirtualnych jest odradzane, ponieważ spowoduje zablokowanie dysku maszyny Wirtualnej. Zaleca się dokładne przejrzenie dokumentacji i przetestowanie scenariuszy przed zablokowaniem zasad opartych na czasie.

**Czy muszę utworzyć nowe konto magazynu, aby korzystać z tej funkcji?**

Nie, można użyć niezmiennego magazynu z istniejącymi lub nowo utworzonymi kontami ogólnego przeznaczenia v1, ogólnego przeznaczenia v2, BlobStorage lub BlockBlobStorage. Obsługiwane są konta magazynu ogólnego przeznaczenia w wersji 1, ale zaleca się uaktualnienie do wersji 2 ogólnego przeznaczenia, dzięki czemu można korzystać z większej liczby funkcji. Aby uzyskać informacje na temat uaktualniania istniejącego konta magazynu ogólnego przeznaczenia w wersji 1, zobacz [Uaktualnianie konta magazynu](../common/storage-account-upgrade.md).

**Czy mogę zastosować zarówno zasady przechowywania blokady prawnej, jak i czasowej?**

Tak, kontener może mieć jednocześnie zarówno blokadę prawną, jak i zasadę przechowywania opartą na czasie; jednak ustawienie "allowProtectedAppendWrites" nie będzie stosowane, dopóki nie zostanie wyczyszczone wstrzymanie prawne. Wszystkie obiekty blob w tym kontenerze pozostają w stanie niezmiennym do momentu usunięcia stanu archiwizacji ze względów prawnych nawet po upływie obowiązującego okresu przechowywania. I odwrotnie — obiekty blob pozostają w stanie niezmiennym przez cały obowiązujący okres przechowywania nawet po usunięciu stanu archiwizacji ze względów prawnych. 

**Czy zasady legalnego przechowywania są prowadzone wyłącznie w postępowaniach sądowych, czy też istnieją inne scenariusze użycia?**

Nie, Wstrzymanie prawne jest tylko ogólnym terminem używanym dla zasad przechowywania nieopartych na czasie. Nie musi być on stosowany wyłącznie w postępowaniach sądowych. Zasady wstrzymania prawnego są przydatne do wyłączania zastępowania i usuwania w celu ochrony ważnych danych WORM przedsiębiorstwa, gdzie okres przechowywania jest nieznany. Można go używać jako zasady przedsiębiorstwa, aby chronić krytyczne obciążenia WORM o znaczeniu krytycznym lub użyć go jako zasady przejściowej, zanim niestandardowy wyzwalacz zdarzeń wymaga użycia zasad przechowywania opartych na czasie. 

**Czy mogę usunąć _zablokowane_ zasady przechowywania oparte na czasie lub blokadę prawną?**

Z kontenera można usunąć tylko odblokowane zasady przechowywania oparte na czasie. Po zablokowaniu zasad przechowywania opartych na czasie nie można jej usunąć; dozwolone są tylko efektywne przedłużenie okresu przechowywania. Prawne tagi wstrzymania można usunąć. Po usunięciu wszystkich tagów prawnych blokada prawna jest usuwana.

**Co się stanie, jeśli spróbuję usunąć kontener z zasadami przechowywania opartymi na czasie lub blokadą prawną?**

Operacja Usuń kontener zakończy się niepowodzeniem, jeśli w kontenerze istnieje co najmniej jeden obiekt blob z zablokowanymi lub odblokowanymi zasadami przechowywania opartymi na czasie lub jeśli kontener ma blokadę prawną. Operacja Usuń kontenera zakończy się pomyślnie tylko wtedy, gdy w kontenerze nie istnieją żadne obiekty BLOB i nie ma żadnych blokad prawnych. 

**Co się stanie, jeśli spróbuję usunąć konto magazynu w kontenerze, który ma zasadę przechowywania opartą na czasie lub blokadę prawną?**

Usunięcie konta magazynu zakończy się niepowodzeniem, jeśli istnieje co najmniej jeden kontener z ustawowym zestawem blokad lub **zablokowanymi** zasadami opartymi na czasie. Kontener z odblokowaną zasadą opartą na czasie nie chroni przed usunięciem konta magazynu. Przed usunięciem konta magazynu należy usunąć wszystkie **blokady** prawne i usunąć wszystkie zablokowane kontenery. Aby uzyskać informacje na temat usuwania kontenera, zobacz poprzednie pytanie. Można również zastosować dalsze zabezpieczenia usuwania konta magazynu za pomocą [blokad usługi Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**Czy mogę przenieść dane w różnych warstwach obiektów blob (gorący, chłodny, archiwum), gdy obiekt blob jest w stanie niezmiennym?**

Tak, można użyć polecenia Set Blob Tier, aby przenieść dane między warstwami obiektów blob przy zachowaniu danych w zgodnym stanie niezmiennym. Niezmienne przechowywanie jest obsługiwane w warstwach gorąca, chłodna i archiwum obiektów blob.

**Co się stanie, jeśli nie uiszczę opłaty, a okres przechowywania jeszcze nie wygasł?**

W przypadku braku płatności obowiązują normalne zasady przechowywania danych zgodnie z warunkami umowy z firmą Microsoft. Aby uzyskać ogólne informacje, zobacz [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Czy jest oferowany okres próbny, umożliwiający przetestowanie tej funkcji?**

Tak. Po utworzeniu zasady przechowywania opartej na czasie jest w stanie *odblokowania.* W tym stanie można wprowadzić wszelkie pożądane zmiany w interwale przechowywania, takie jak zwiększenie lub zmniejszenie, a nawet usunąć zasady. Po zablokowaniu zasad pozostaje zablokowany, dopóki nie wygaśnie interwał przechowywania. Ta zablokowana zasada zapobiega usuwaniu i modyfikowaniu interwału przechowywania. Zdecydowanie zaleca się używanie stanu *odblokowanego* tylko w celach próbnych i blokowanie zasad w ciągu 24 godzin. Praktyki te pomagają w przestrzeganiu sec 17a-4(f) i innych przepisów.

**Czy mogę używać usuwania nietrwałego obok zasad niezmiennych obiektów blob?**

Tak, jeśli wymagania dotyczące zgodności zezwalają na usuwanie nietrwałe. [Usuwanie nietrwałe dla usługi Azure Blob storage](storage-blob-soft-delete.md) ma zastosowanie do wszystkich kontenerów w ramach konta magazynu, niezależnie od zasad przechowywania prawnych lub opartych na czasie. Zalecamy włączenie usuwania nietrwałego w celu dodatkowej ochrony przed zastosowaniem i potwierdzeniem jakichkolwiek niezmiennych zasad WORM.

## <a name="next-steps"></a>Następne kroki

- [Ustawianie zasad niezmienności dla magazynu obiektów Blob i zarządzanie nimi](storage-blob-immutability-policies-manage.md)
- [Ustawianie reguł w celu automatycznego warstwii i usuwania danych obiektów blob za pomocą zarządzania cyklem życia](storage-lifecycle-management-concepts.md)
- [Soft delete for Azure Storage blobs](../blobs/storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)
- [Chroń subskrypcje, grupy zasobów i zasoby za pomocą blokad usługi Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
