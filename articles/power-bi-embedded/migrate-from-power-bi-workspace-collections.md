---
title: Jak przeprowadzić migrację kolekcji obszarów roboczych usługi Power BI zawartości do Power BI Embedded | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację z programu Power BI obszaru roboczego kolekcji do Power BI Embedded i wykorzystać przechodzi do osadzenia w aplikacji.
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Jak przeprowadzić migrację kolekcji obszarów roboczych usługi Power BI zawartości do Power BI Embedded

Dowiedz się, jak przeprowadzić migrację z programu Power BI obszaru roboczego kolekcji do Power BI Embedded. Ten artykuł zawiera wskazówki dotyczące migracji z kolekcji obszaru roboczego Azure Power BI, aby usługa Power BI Embedded. Ponadto opisano, czego można oczekiwać podczas zmian w aplikacji.

Power BI obszaru roboczego kolekcje zasobu jest nadal dostępne przez ograniczony czas, po wersji ogólnodostępnej dodatku Power BI. Klienci, w ramach umowy Enterprise Agreement mają dostęp do ich istniejących zbiorach obszaru roboczego do wygaśnięcia ich istniejące umowy. Klienci, którzy nabyte Power BI obszaru roboczego kolekcje kanałami bezpośrednio lub dostawcy usług Kryptograficznych korzysta dostępu przez jeden rok z ogólne dostępności programu Power BI Premium.

> [!IMPORTANT]
> Podczas migracji ma zależność usługi Power BI, nie ma zależności usługi Power BI dla użytkowników aplikacji przy użyciu **osadzić token**. Nie potrzebują zalogowania się do usługi Power BI wyświetlić zawartość osadzonego w aplikacji. Możesz użyć tej funkcji osadzanie podejście osadzić usługi Power BI dla klientów.

![Power BI Embedded przepływu](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Przygotowanie do migracji

Istnieje kilka rzeczy, które należy wykonać w celu przygotowania do migracji z usługi Power BI obszaru roboczego kolekcje za pośrednictwem do Power BI Embedded. Należy dzierżawcy dostępne razem z użytkownikiem, które ma licencję usługi Power BI Pro.

1. Upewnij się, że masz dostęp do dzierżawy usługi Azure Active Directory (Azure AD).

    Dzierżawy, których można użyć?

    * Użyj istniejącej dzierżawy usługi Power BI firmy?
    * Użyj oddzielnych dzierżawy dla aplikacji?
    * Dla każdego klienta, należy użyć oddzielnych dzierżawy?

    Jeśli chcesz utworzyć nową dzierżawę dla aplikacji lub każdego klienta, zobacz jedną z następujących czynności:

    * [Tworzenie dzierżawy usługi Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Jak uzyskać dzierżawę usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Utwórz użytkownika w ramach tej nowej dzierżawy, który działa jako konto "główny" aplikacji. Czy to konto wymaga zalogowania się do usługi Power BI i musi mieć licencję usługi Power BI Pro przypisane do niej.

## <a name="accounts-within-azure-ad"></a>Konta w usłudze Azure AD

Następujące konta muszą istnieć w dzierżawie.

> [!NOTE]
> Te konta muszą mieć licencji usługi Power BI Pro, aby można było używać aplikacji obszarów roboczych.

1. Administrator dzierżawy.

    Zaleca się, że osadzania obszaru roboczego aplikacji ma Administrator dzierżawy wymieniony jako element członkowski.

2. Konta dla analityków, które tworzenia zawartości.

    Ci użytkownicy powinni należeć obszarów roboczych aplikacji zgodnie z potrzebami.

3. Aplikacja *wzorca* konta użytkownika lub konta usługi.

    Zaplecze aplikacji przechowuje poświadczenia dla tego konta. Użyj *wzorca* konta uzyskania tokenu usługi Azure AD do użytku z interfejsami API REST usługi Power BI. To konto jest używane do generowania tokenu osadzania dla aplikacji. *Wzorca* konto musi być administratorem obszarów roboczych aplikacji utworzone do osadzania.

    **To konto jest tylko konta zwykłych użytkowników w organizacji, która jest używana do celów osadzenia.**

## <a name="app-registration-and-permissions"></a>Rejestracja aplikacji i uprawnień

W celu wykonywania wywołań interfejsu API REST, należy zarejestrować aplikację w usłudze Azure AD. Konfiguracja dodatku jest stosowany w portalu Microsoft Azure oprócz strony rejestracji aplikacji w usłudze Power BI. Aby uzyskać więcej informacji, zobacz [zarejestrować aplikację usługi Azure AD do osadzenia zawartość usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Zalecane jest, aby zarejestrować aplikację za pomocą aplikacji **wzorca** konta.

## <a name="create-app-workspaces-required"></a>Tworzenie obszarów roboczych aplikacji (wymagany)

Jeśli aplikacja jest obsługi wielu klientów, możesz korzystać z obszarów roboczych aplikacji w celu zapewnienia lepszej izolacji. Pulpity nawigacyjne i raporty będą odizolowane od klientów. Następnie można użyć konta usługi Power BI na obszar roboczy aplikacji do dalszego izolowania aplikacji środowiska między klientami, ale możesz po prostu używać jednego konta do uproszczenia.

> [!IMPORTANT]
> Nie można użyć osobistego obszaru roboczego ("Mój obszar roboczy") Aby skorzystać z osadzania dla klientów.

Należy użytkownik, który ma licencję Pro, aby można było utworzyć obszaru roboczego aplikacji w usłudze Power BI. Użytkownik usługi Power BI, który tworzy obszaru roboczego aplikacji jest administratorem tego obszaru roboczego domyślnie. **Aplikacja *wzorca* konto musi być administratorem obszaru roboczego.**

## <a name="content-migration"></a>Migracji zawartości

Migrowanie zawartości z kolekcji obszaru roboczego do Power BI Embedded może odbywać się równolegle do bieżącego rozwiązania i nie wymaga żadnych przestojów.

A **narzędzia migracji** jest dostępny do użycia, aby ułatwić kopiowania zawartości z kolekcji obszaru roboczego programu Power BI do Power BI Embedded. Zwłaszcza, jeśli masz wiele raportów. Aby uzyskać więcej informacji, zobacz [narzędzie migracji usługi Power BI Embedded](migrate-tool.md).

Migracji zawartości opiera się głównie na dwóch interfejsów API.

1. Pobierz plik PBIX - tego interfejsu API można pobrać plików PBIX, które zostały przekazane do usługi Power BI po października 2016 r.
2. Importuj plik PBIX — ten interfejs API przekazuje wszystkie PBIX do usługi Power BI.

Niektóre związanych z fragmentów kodu, zobacz [kodu wstawki migrowania zawartości z Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Typy raportów

Istnieje kilka typów raportów, wymagających przepływu różnych migracji.

#### <a name="cached-dataset-and-report"></a>Zestaw danych w pamięci podręcznej i raport

Pamięci podręcznej zestawów danych można znaleźć plików PBIX, które było zaimportować danych, w przeciwieństwie do połączenia na żywo lub połączenie zapytania bezpośredniego.

**Flow**

1. Wywołania API Pobierz plik PBIX z obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
2. Zapisz plik PBIX.
3. Wywołania PBIX importu dla obszaru roboczego funkcji Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Zestaw danych zapytania bezpośredniego i raport

**Flow**

1. Wywołanie GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources i zapisać parametry połączenia odebrane.
2. Wywołania API Pobierz plik PBIX z obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
3. Zapisz plik PBIX.
4. Wywołania PBIX importu dla obszaru roboczego funkcji Power BI Embedded.
5. Zaktualizuj parametry połączenia przez wywołanie — POST  https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. Pobierz identyfikator identyfikator GW i źródła danych przez wywołanie metody — pobranie https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Aktualizacja poświadczeń użytkownika, wywołując — poprawki https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Stary zestaw danych i raportów

Raporty przekazany, zanim października 2016 nie obsługuje funkcji Pobierz plik PBIX.

**Flow**

1. Pobierz plik PBIX z środowiska projektowego (kontroli źródła wewnętrznego).
2. Wywołania PBIX importu dla obszaru roboczego funkcji Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Wypychanie zestawu danych i raportów

Pobierz plik PBIX nie obsługuje *Push interfejsu API* zestawów danych. Przekazywaniu interfejsu API zestawu danych danych nie może być przenoszone z kolekcji obszaru roboczego programu Power BI do Power BI Embedded.

**Flow**

1. Wywołanie "Utwórz zestaw danych" interfejsu API z zestawem danych Json można utworzyć zestawu danych obszaru roboczego Power BI Embedded.
2. Skompiluj ponownie raport dla zestawu danych utworzone *.

Istnieje możliwość migracji powiadomienia wypychanego za pomocą rozwiązania niektórych raportu interfejsu api programu Power BI obszaru roboczego kolekcje na Power BI Embedded podejmując następujące:

1. Przekazywanie niektórych fikcyjny PBIX do obszaru roboczego kolekcji obszarów roboczych usługi Power BI.
2. Klonowanie naciśnięcie interfejsu api raportu i powiąż go fikcyjny PBIX z kroku 1.
3. Pobierz wypychania raportowania interfejsu API z fikcyjny plik PBIX.
4. Przekaż fikcyjny PBIX do obszaru roboczego funkcji Power BI Embedded.
5. Tworzenie zestawu wypychania danych w usłudze Power BI Embedded obszaru roboczego.
6. Ponownie powiązać raportu, aby wypychać interfejsu api zestawu danych.

## <a name="create-and-upload-new-reports"></a>Tworzenie i przekazywanie nowe raporty

Oprócz zawartość, którą migrowane z kolekcji obszaru roboczego programu Power BI możesz tworzenie raportów i zestawów danych używających Power BI Desktop, a następnie opublikuj tych raportów do obszaru roboczego aplikacji. Użytkownik końcowy publikowania raporty konieczne musi mieć licencję usługi Power BI Pro, aby opublikować aplikację obszaru roboczego.

## <a name="rebuild-your-application"></a>Aplikacja jest ponownie kompilowana

1. Modyfikowanie aplikacji używać interfejsów API REST Power BI i lokalizację raportu w witrynie powerbi.com.

2. Odbuduj swoją authn w zakresie/autoryzacja uwierzytelniania przy użyciu *wzorca* konta dla aplikacji. Użytkownik może skorzystać z przy użyciu [osadzić token](https://msdn.microsoft.com/library/mt784614.aspx) Aby zezwolić użytkownikowi na działanie w imieniu innych użytkowników.

3. Osadź raportów w usłudze Power BI Embedded do aplikacji. Aby uzyskać więcej informacji, zobacz [jak osadzić usługi Power BI pulpity nawigacyjne, raporty i programu Kafelki](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapowanie użytkowników do użytkowników usługi Power BI

W aplikacji, użytkowników, którymi można zarządzać w aplikacji należy mapować *wzorca* poświadczenia usługi Power BI na potrzeby aplikacji. Poświadczenia dla tej usługi Power BI *wzorca* konta są przechowywane w aplikacji i służyć do tworzenia osadzaj tokenów.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Co zrobić, gdy wszystko będzie gotowe do produkcji

Gdy wszystko będzie gotowe do przeniesienia do produkcji, należy wykonać następujące czynności:

- Jeśli korzystasz z osobnych dzierżawy do tworzenia aplikacji, musisz upewnij się, że aplikacja obszarów roboczych, wraz z pulpitami nawigacyjnymi i raportami, są dostępne w środowisku produkcyjnym. Upewnij się, tworzenia aplikacji w usłudze Azure AD dla dzierżawy produkcji, a następnie przypisać uprawnienia właściwej aplikacji, jak wskazano w kroku 1.

- Zakup pojemności, który odpowiada Twoim potrzebom. Można użyć [planowanie oficjalny dokument pojemności analytics osadzone](https://aka.ms/pbiewhitepaper) ułatwi zrozumienie, co może być konieczne. Gdy wszystko będzie gotowe do zakupu, możesz kupić Power BI Embedded zasobów w portalu Azure.

- Edytuj obszar roboczy aplikacji i przypisz je do pojemności w obszarze Zaawansowane.

    ![Przypisz obszaru roboczego aplikacji do pojemności w witrynie powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Wdróż zaktualizowane aplikacji do środowiska produkcyjnego i rozpocząć osadzania raportów w usłudze Power BI Embedded.

## <a name="after-migration"></a>Po migracji

Porządkowanie jest potrzebne w kolekcjach obszaru roboczego programu Power BI.

- Usuń wszystkie obszary robocze wylogowuje wdrożone rozwiązanie w usłudze Azure kolekcji obszaru roboczego programu Power BI.
- Usuń wszystkie kolekcje obszaru roboczego, który istnieje w systemie Azure.

## <a name="next-steps"></a>Kolejne kroki

Gratulacje. Aplikacja jest teraz migracji do Power BI Embedded. Aby dowiedzieć się, jak osadzić z pulpitów nawigacyjnych usługi Power BI, raporty i zestawy danych, zobacz [jak osadzić usługi Power BI pulpity nawigacyjne, raporty i programu Kafelki](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Masz więcej pytań? [Spróbuj skorzystać z społeczności Power BI](http://community.powerbi.com/)