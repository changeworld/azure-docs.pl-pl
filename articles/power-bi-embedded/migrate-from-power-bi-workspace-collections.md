---
title: Jak migrować zawartość kolekcji obszarów roboczych usługi Power BI do Power BI Embedded | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację z kolekcji obszarów roboczych usługi Power BI do Power BI Embedded i wykorzystaj korzyści związane z osadzaniem zawartości w aplikacjach.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: de20d532112ca73f34f7cb603d043579c28179d6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071236"
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Jak migrować zawartość kolekcji obszarów roboczych usługi Power BI do Power BI Embedded

Dowiedz się, jak przeprowadzić migrację z kolekcji obszarów roboczych usługi Power BI do Power BI Embedded. Ten artykuł zawiera wskazówki dotyczące migracji z kolekcji obszarów roboczych usługi Azure Power BI do Power BI Embedded. Również przyjrzymy się co do których można spodziewać się zmian w aplikacji.

Zasób kolekcji obszarów roboczych usługi Power BI w dalszym ciągu być dostępna przez ograniczony czas po wersji ogólnej dostępności usługi Power BI Premium. Klienci objęci umową Enterprise Agreement mają dostęp do swoich istniejących kolekcji obszarów roboczych do czasu wygaśnięcia aktualnych umów. Klienci, którzy uzyskali kolekcji obszarów roboczych usługi Power BI za pośrednictwem kanałów agentów bezpośrednich lub dostawcy usług Kryptograficznych korzystaj z dostępu przez rok od ogólna dostępność programu Power BI Premium.

> [!IMPORTANT]
> Podczas migracji ma zależność od usługi Power BI, nie ma zależność od usługi Power BI dla użytkowników aplikacji przy użyciu **tokenu osadzania**. Nie ma potrzeby się zarejestrować w usłudze Power BI do wyświetlenia zawartości osadzonej w aplikacji. Możesz użyć tej funkcji osadzania podejście osadzanie usługi Power BI dla klientów.

![Przepływ Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Przygotowanie do migracji

Istnieje kilka rzeczy, które należy wykonać przygotowania do migracji z kolekcji obszarów roboczych usługi Power BI usługi za pośrednictwem do Power BI Embedded. Konieczne jest dostępna dzierżawa oraz użytkowników, które ma licencję usługi Power BI Pro.

1. Upewnij się, że masz dostęp do dzierżawy usługi Azure Active Directory (Azure AD).

    Które dzierżawy do użycia?

    * Użyj istniejącej dzierżawy usługi Power BI firmy?
    * Użyć oddzielna dzierżawa dla aplikacji?
    * Użyj oddzielna dzierżawa dla każdego klienta?

    Jeśli zdecydujesz się utworzyć nową dzierżawę dla aplikacji lub dla każdego klienta, zobacz jeden z następujących czynności:

    * [Tworzenie dzierżawy usługi Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Jak uzyskać dzierżawę usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Utwórz użytkownika w ramach tej nowej dzierżawy, który działa jako konto "główną" aplikacji. Czy to konto należy utworzyć konto w usłudze Power BI i musi mieć przypisaną do niego licencję usługi Power BI Pro.

## <a name="accounts-within-azure-ad"></a>Konta w usłudze Azure AD

Następujące konta muszą istnieć w ramach dzierżawy.

> [!NOTE]
> Te konta muszą mieć licencje usługi Power BI Pro, aby można było używać obszary robocze aplikacji.

1. Użytkownik będący administratorem dzierżawy.

    Zaleca się, że osadzania obszar roboczy aplikacji ma administratora dzierżawy na liście jako członka.

2. Konta analityków tworzących zawartość.

    Ci użytkownicy powinni należeć obszarów roboczych aplikacji stosownie do potrzeb.

3. Aplikacja *wzorca* konta użytkownika lub konta usługi.

    Zaplecza aplikacji są przechowywane poświadczenia dla tego konta. Użyj *wzorca* konta w celu uzyskania tokenu usługi Azure AD do użycia z interfejsów API REST usługi Power BI. To konto jest używane do wygenerowania tokenu osadzania dla aplikacji. *Wzorca* konto musi być też administratorem obszarów roboczych aplikacji tworzonych na potrzeby osadzania.

    **To konto jest po prostu to zwykłe konto użytkownika w Twojej organizacji, który jest używany na potrzeby osadzania.**

## <a name="app-registration-and-permissions"></a>Rejestrowanie aplikacji i uprawnienia

Wykonywanie wywołań interfejsu API REST, należy zarejestrować aplikację w usłudze Azure AD. Konfiguracja dodatku jest stosowana w portalu Microsoft Azure oprócz stronie rejestracji aplikacji Power BI. Aby uzyskać więcej informacji, zobacz [rejestrowania aplikacji usługi Azure AD, aby osadzić zawartość usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Zalecane jest, aby zarejestrować aplikację za pomocą aplikacji **wzorca** konta.

## <a name="create-app-workspaces-required"></a>Tworzenie obszarów roboczych aplikacji (wymagane)

Jeśli aplikacja obsługuje wielu klientów, możesz skorzystać z obszarów roboczych aplikacji w celu zapewnienia lepszej izolacji. Pulpity nawigacyjne i raporty będą izolowane od klientów. Konto usługi Power BI może następnie użyć każdego obszaru roboczego aplikacji, aby dodatkowo odizolować środowiska poszczególnych klientów, ale możesz po prostu używać jednego konta w celu uproszczenia go.

> [!IMPORTANT]
> Nie można używać osobistego obszaru roboczego ("Mój obszar roboczy") może korzystać z osadzania dla swoich klientów.

Należy użytkownika, które ma licencję Pro, aby można było utworzyć obszaru roboczego aplikacji w usłudze Power BI. Użytkownik usługi Power BI, który tworzy obszar roboczy aplikacji jest administratorem tego obszaru roboczego domyślnie. **Aplikacja *wzorca* konto musi być kontem administratora obszaru roboczego.**

## <a name="content-migration"></a>Migracja zawartości

Migrację zawartości z kolekcji obszarów roboczych do Power BI Embedded może odbywać się równolegle z korzystaniem z bieżącego rozwiązania i nie wymaga żadnych przestojów.

A **narzędzie do migracji** jest dostępna do użycia, ułatwiającego kopiowanie zawartości z kolekcji obszarów roboczych usługi Power BI do Power BI Embedded. Zwłaszcza, jeśli masz wiele raportów. Aby uzyskać więcej informacji, zobacz [narzędzie migracji usługi Power BI Embedded](migrate-tool.md).

Migracja zawartości opiera się przede wszystkim na dwóch interfejsach API.

1. Interfejs API pobierania można pobrać pliki PBIX, które zostały przekazane do usługi Power BI od października 2016 r.
2. "Import pbix" ten interfejs API przekazuje wszelkie pliki PBIX do usługi Power BI.

Aby związanych z fragmentów kodu, zobacz [fragmenty kodu umożliwiające migrację zawartości z Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Typy raportów

Istnieje kilka typów raportów, które wymagają innej procedury migracji.

#### <a name="cached-dataset-and-report"></a>Raport i zestaw danych w pamięci podręcznej

Buforowane zestawy danych można znaleźć pliki PBIX zawierające zaimportowane dane, a nie połączenie na żywo czy połączenie zapytania bezpośredniego.

**Przepływ**

1. Wywołania API pobierania pliku PBIX z obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
2. Zapisz plik PBIX.
3. Wywołaj Importowanie pliku obszaru roboczego usługi Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Raport i zestaw danych zapytania bezpośredniego

**Przepływ**

1. Wywołaj metodę GET `https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources` i Zapisz odebrane parametry połączenia.
2. Wywołania API pobierania pliku PBIX z obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
3. Zapisz plik PBIX.
4. Wywołaj Importowanie pliku obszaru roboczego usługi Power BI Embedded.
5. Zaktualizuj parametry połączenia za pośrednictwem wywołania — POST  `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections`
6. Pobierz identyfikator identyfikator bramy i źródła danych, wywołując — Pobierz `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources`
7. Zaktualizuj poświadczenia użytkownika, wywołując — PATCH `https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}`

#### <a name="old-dataset-and-reports"></a>Stary zestaw danych i raportów

Raporty przekazany, zanim października 2016 r. nie obsługują funkcji Pobierz plik PBIX. 

**Przepływ**

1. Pobierz plik PBI ze środowiska deweloperskiego (wewnętrznej kontroli źródła).
2. Wywołaj Importowanie pliku obszaru roboczego usługi Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Raport i zestaw danych wypychania

Pobierz plik PBIX nie obsługuje *interfejsu API wypychania* zestawów danych. Wypychanie zestawem danych interfejsu API, których nie można przenosić danych z kolekcji obszarów roboczych usługi Power BI do Power BI Embedded.

**Przepływ**

1. Wywołanie API "Create dataset" z zestawem danych Json, aby utworzyć zestaw danych obszaru roboczego usługi Power BI Embedded.
2. Skompiluj ponownie raport dla utworzonego zestawu danych *.

Można użyć obejścia wypychania migracji raportu interfejsu api z kolekcji obszarów roboczych usługi Power BI do Power BI Embedded, wykonując następujące czynności:

1. Przekazywanie dowolny zastępczy plik PBIX do obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
2. Klonowanie wypychania interfejsu api raportów i powiązać go z zastępczym plikiem pbix z kroku 1.
3. Pobierz raport interfejsu API z zastępczym plikiem PBIX wypychania.
4. Przekaż zastępczy plik PBIX do obszaru roboczego usługi Power BI Embedded.
5. Utwórz zestaw danych wypychania w obszarze roboczym usługi Power BI Embedded.
6. Ponownie Powiąż raport z zestawem danych interfejsu api wypychania.

## <a name="create-and-upload-new-reports"></a>Tworzenie i przekazywanie nowych raportów

Oprócz zawartości migrowanej z kolekcji obszarów roboczych usługi Power BI możesz tworzyć raporty i zestawy danych przy użyciu usługi Power BI Desktop, a następnie publikować te raporty w obszarze roboczym aplikacji. Użytkownik końcowy Publikujący raportów muszą mieć licencję usługi Power BI Pro, aby publikować w obszarze roboczym aplikacji.

## <a name="rebuild-your-application"></a>Ponowne kompilowanie aplikacji

1. Zmodyfikuj aplikacja korzysta z interfejsów API REST usługi Power BI oraz lokalizacji raportu w witrynie powerbi.com.

2. Ponownie skompiluj swoją uwierzytelnianie AuthN/AuthZ uwierzytelniania przy użyciu *wzorca* konta aplikacji. Można skorzystać z [tokenu osadzania](https://msdn.microsoft.com/library/mt784614.aspx) Aby zezwolić temu użytkownikowi na działanie w imieniu innych użytkowników.

3. Możesz osadzać raporty z Power BI Embedded w aplikacji. Aby uzyskać więcej informacji, zobacz [jak osadzić pulpity nawigacyjne usługi Power BI, raportów i kafelków](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapowanie użytkowników na użytkownika usługi Power BI

W aplikacji, możesz mapować użytkowników, którymi zarządzasz, w ramach aplikacji *wzorca* poświadczenia usługi Power BI na potrzeby aplikacji. Poświadczenia dla tej usługi Power BI *wzorca* konta są przechowywane w ramach aplikacji i używane do tworzenia tokenów osadzania.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Co należy zrobić, gdy wszystko będzie gotowe do produkcji

Gdy wszystko jest gotowe do przejścia do środowiska produkcyjnego, należy wykonać następujące czynności:

- Korzystając z oddzielnej dzierżawy do tworzenia aplikacji, należy się upewnić, że Twoje obszary robocze aplikacji oraz pulpity nawigacyjne i raporty są dostępne w środowisku produkcyjnym. Upewnij się, że utworzono aplikację w usłudze Azure AD dla dzierżawy produkcyjnej i przypisać odpowiednie uprawnienia aplikacji, jak wskazano w kroku 1.

- Kup pojemność, która spełnia Twoje potrzeby. Możesz użyć [oficjalny dokument dotyczący planowania pojemności na potrzeby analizy osadzonej](https://aka.ms/pbiewhitepaper) pomagające zrozumieć, które mogą wymagać. Gdy wszystko jest gotowe do zakupu, możesz kupić zasobem Power BI Embedded w witrynie Azure portal.

- Edytuj obszar roboczy aplikacji i przypisz go do pojemności w obszarze Zaawansowane.

    ![Przypisz obszar roboczy aplikacji do pojemności w witrynie powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Wdróż zaktualizowaną aplikację w środowisku produkcyjnym i Rozpocznij osadzanie raportów z Power BI Embedded.

## <a name="after-migration"></a>Po zakończeniu migracji

Niektóre oczyszczania jest wymagany w obrębie kolekcji obszarów roboczych usługi Power BI.

- Usuń wszystkie obszary robocze poza wdrożonym rozwiązaniem w ramach usługi platformy Azure kolekcje obszarów roboczych usługi Power BI.
- Usuń wszelkie kolekcje obszarów roboczych, które istnieją w obrębie platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Gratulacje. Aplikacja jest teraz migrowana do Power BI Embedded. Aby dowiedzieć się, jak osadzić pulpity nawigacyjne usługi Power BI, raporty i zestawy danych, zobacz [jak osadzić pulpity nawigacyjne usługi Power BI, raportów i kafelków](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Masz więcej pytań? [Zadaj pytanie społeczności usługi Power BI](http://community.powerbi.com/)