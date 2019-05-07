---
title: Instalowanie i konfigurowanie usługi AWS kosztów i użycia integracji raportu z usługi Azure Cost Management
description: W tym artykule opisano, chociaż instalowania i konfigurowania usług AWS kosztów i użycia integracji raportu z usługi Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7a020284f44eda0da62f307866c74b0a8df493d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205696"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Instalowanie i konfigurowanie usługi AWS kosztów i użycia integracji raportu

Dzięki integracji raportu Amazon Web Services kosztów i użycia można monitorować i kontrolować AWS wydatki w usłudze Azure Cost Management. Integracja umożliwia jednej lokalizacji w witrynie Azure portal, gdzie można monitorować i kontroli wydatki na usługi AWS i Azure. W tym artykule opisano sposób konfigurowania integracji i skonfiguruj ją tak, przy użyciu funkcji Cost Management analizując koszty i przejrzyj budżetów.

Koszt procesów zarządzania raportu AWS kosztów i użycia, przechowywane w przedział S3 przy użyciu poświadczeń dostępu AWS, aby uzyskać definicje raportów i Pobierz raport, który pliki GZIP CSV.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Tworzenie raportu kosztów i użycia w usłudze AWS

Korzystanie z raportów kosztów i użycia jest zalecany sposób AWS do zebrania i przetworzenia koszty usług AWS. Aby uzyskać więcej informacji, zobacz [koszty usług AWS i raport użycia](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) artykułem dokumentacji.

Użyj **raporty** strony do rozliczeń i zarządzania kosztami konsoli w usłudze AWS, aby utworzyć raport kosztów i użycia wykonując następujące kroki.

1. Zaloguj się do konsoli zarządzania usług AWS, a następnie otwórz konsolę rozliczeń i zarządzania kosztami na https://console.aws.amazon.com/billing.
2. W okienku nawigacji kliknij **raporty**.
3. Kliknij przycisk **Utwórz raport**.
4. Aby uzyskać **Nazwa raportu**, wpisz nazwę dla raportu.
5. Aby uzyskać **jednostkę czasu**, wybierz **godzinowe**.
6. Dla **Include**Dodaj identyfikatory poszczególnych zasobów w raporcie i wybierz **identyfikatorów zasobów**.
7. Aby uzyskać **włączyć obsługę**, zaznaczenie nie jest konieczne.
8. Dla **ustawienia odświeżania danych**, wybierz opcję **automatyczne odświeżanie koszt &amp; raport użycia po wykryciu opłaty ostatnich miesięcy z zamknięty rachunki**.
9. Kliknij przycisk **Dalej**.
10. Aby uzyskać **przedział Amazon S3**, wpisz nazwę zasobnika Amazon S3, której chcesz raporty dostarczane do i kliknij przycisk **Sprawdź, czy**. Pakietu musi mieć odpowiednie uprawnienia, była ważna. Aby uzyskać więcej informacji na temat dodawania uprawnień do zasobnika zobacz [zasobnika ustawienia i uprawnienia dostępu do obiektu](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Aby uzyskać **prefiks ścieżki raportu**, wpisz prefiks Ścieżka raportu, który ma zostać dodane do nazwy raportu.
12. Aby uzyskać **kompresji**, wybierz opcję **GZIP**.
13. Kliknij przycisk **Dalej**.
14. Po przejrzeniu ustawienia dla raportu, kliknij przycisk **Przejrzyj i Zakończ**.
    Uwaga **Nazwa raportu**. Zostanie użyty w kolejnych krokach.

Może upłynąć do 24 godzin usługi AWS rozpocząć dostarczanie raportów do zasobnika usługi Amazon S3. Po rozpoczęciu dostarczania usług AWS aktualizuje pliki raportu usługi AWS kosztów i użycia co najmniej raz dziennie. Możesz kontynuować konfigurowanie środowiska usługi AWS bez oczekiwania na dostarczanie rozpocząć.

## <a name="create-a-role-and-policy-in-aws"></a>Tworzenie ról i zasad w usłudze AWS

Usługa Azure Cost Management uzyskuje dostęp do przedział S3, w którym raport kosztów i użycia znajduje się kilka razy dziennie. Usługa Cost Management wymaga dostępu do poświadczeń w celu wyszukiwania nowych danych. Rola i zasady są tworzone w usłudze AWS, aby zezwolić na dostęp przez usługę Cost Management.

Aby włączyć opartej na rolach dostęp do konta usługi AWS w usłudze Azure Cost Management, rola zostanie utworzona w konsoli usług AWS. Musisz mieć _ARN roli_ i _Identyfikatora zewnętrznego_ za pomocą konsoli usług AWS. Później będziesz ich używać w tworzenie strony łącznika usługi AWS w usłudze Azure Cost Management.

Użyj nowego kreatora roli tworzenia:

1. Zaloguj się do konsoli usług AWS i wybierz **usług**.
2. Na liście usług wybierz **IAM**.
3. Wybierz **role** a następnie kliknij przycisk **Utwórz rolę**.
4. Na następnej stronie wybierz **konta usług AWS innego**.
5. W **Accountid** , wprowadź _432263259397_.
6. W **opcje**, wybierz opcję **wymagają Identyfikatora zewnętrznego (najlepsze rozwiązanie, gdy innej założy, ta rola)**.
7. W **Identyfikatora zewnętrznego**, wprowadź nazwę zewnętrznego. Identyfikator zewnętrzny jest udostępniony kod dostępu między roli usług AWS i Azure Cost Management. Na stronie Nowy łącznik w Cost Management służy także tego samego Identyfikatora zewnętrznego. Na przykład Identyfikator zewnętrzny przypomina _Companyname1234567890123_.
    Nie zmieniaj wybór **wymagają usługi MFA**. Powinny pozostać wyczyszczone.
8. Kliknij pozycję **Next: Permissions** (Dalej: uprawnienia).
9. Kliknij przycisk **Tworzenie zasad**. Nowa karta przeglądarki zostanie otwarty, gdzie możesz utworzyć nowe zasady.
10. Kliknij przycisk **wybierz usługę**.

Skonfiguruj uprawnienia kosztów i użycia raportu:

1. Typ **kosztów i raport użycia**.
2. Wybierz **poziom dostępu**, **odczytu** > **DescribeReportDefinitions**. Dzięki temu Cost Management przeczytaj, co WALUTA raporty są definiowane i określić, jeśli spełniają warunek wstępny definicji raportu.
3. Kliknij przycisk **Dodaj dodatkowe uprawnienia**.

Konfigurowanie uprawnień obiektów i przedział S3:

1. Kliknij przycisk **wybierz usługę**.
2. Typ _S3_.
3. Wybierz **poziom dostępu**, **listy** > **ListBucket**. Ta akcja pobiera listę obiektów w przedział S3.
4. Wybierz **poziom dostępu**, **odczytu** > **GetObject**. Ta akcja umożliwia rozliczeń pobierania plików.
5. Wybierz **zasobów**.
6. Wybierz **zasobnika — Dodaj ARN**.
7. W **Nazwa zasobnika**, wprowadź przedział używane do przechowywania plików WALUTA.
8. Wybierz **obiekt — Dodaj ARN**.
9. W **Nazwa zasobnika**, wprowadź przedział używane do przechowywania plików WALUTA.
10. W **nazwa obiektu**, wybierz opcję **wszelkie**.
11. Kliknij przycisk **Dodaj dodatkowe uprawnienia**.

Skonfiguruj uprawnienia Explorer kosztów:

1. Kliknij przycisk **wybierz usługę**.
2. Typ _koszt Explorer z_.
3. Wybierz **akcji w przypadku wszystkich koszt Eksploratora usługi (ce:\*)**. Ta akcja sprawdza, czy kolekcja jest poprawna.
4. Kliknij przycisk **Dodaj dodatkowe uprawnienia**.

Dodaj uprawnienie organizacji:

1. Typ **organizacje**.
2. Wybierz **poziom dostępu, na liście** > **wyświetlanie kont**. Ta akcja pobiera nazwy kont.
3. W **zasady przeglądu**, wprowadź nazwę nowych zasad. Zaznacz, aby upewnić się, czy wprowadzono poprawne informacje, a następnie kliknij przycisk **Utwórz zasady**.
4. Wróć do poprzedniej karty, a następnie odśwież stronę sieci web w przeglądarce. W pasku wyszukiwania Wyszukaj nowe zasady.
5. Wybierz **dalej: Przejrzyj**.
6. Wprowadź nazwę dla nowej roli. Zaznacz, aby upewnić się, czy wprowadzono poprawne informacje, a następnie kliknij przycisk **Utwórz rolę**.
    Uwaga **ARN roli** i **Identyfikatora zewnętrznego** używane w poprzednich krokach, podczas tworzenia roli. Użyjesz ich później podczas konfigurowania łącznika usługi Azure Cost Management.

Zasady JSON powinien wyglądać następująco. Zastąp _bucketname_ nazwą Twojego przedział S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Skonfiguruj nowy łącznik usługi AWS na platformie Azure

Skorzystaj z poniższych informacji, aby utworzyć nowy łącznik usługi AWS i rozpocząć monitorowanie koszty usług AWS.

1. Zaloguj się do witryny Azure portal pod https://portal.azure.com.
2. Przejdź do **Cost Management + rozliczenia** > **Cost Management**.
3. W obszarze **ustawienia**, kliknij przycisk **łączników (wersja zapoznawcza) w chmurze**.  
    ![Przykład przedstawiający łączników chmury (w wersji zapoznawczej ustawienie)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Kliknij przycisk **+ Dodaj** w górnej części strony Aby utworzyć nowy łącznik.
5. Twórz strony łącznika usług AWS, wpisz **nazwę wyświetlaną** nazwę łącznika.  
    ![Przykład polecenia Create strony łącznika usług AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcjonalnie wybierz domyślną grupę zarządzania. Wszystkie odnalezione połączone konta zostaną zachowane. Możesz skonfigurować go później.
7. W obszarze **rozliczeń** zaznacz **automatycznie opłata w wysokości 1% po ogólnym udostępnieniu** Jeśli chcesz zapewnić ciągłe działanie, po wygaśnięciu wersji zapoznawczej. Jeśli wybierzesz opcję automatycznego, należy wybrać rozliczeń **subskrypcji**.
8. Wprowadź **roli ARN**. Jest to wartość, która została użyta podczas konfigurowania roli w usłudze AWS.
9. Wprowadź **Identyfikatora zewnętrznego**. Jest to wartość, która została użyta podczas konfigurowania roli w usłudze AWS.
10. Wprowadź **Nazwa raportu** utworzonego w usłudze AWS.
11. Kliknij przycisk **dalej** a następnie kliknij przycisk **Utwórz**.

Może upłynąć kilka godzin nowe zakresy usług AWS, konta usług AWS skonsolidowane i połączonych kont platformy AWS i ich danych rozwiązania cost są wyświetlane.

Po utworzeniu łącznika, firma Microsoft zaleca, przypisywanie kontroli dostępu do łącznika. Użytkownicy mają uprawnienia do nowo odnalezione zakresów: Skonsolidowane usługi AWS, konta i AWS połączone konta. Użytkownik, który tworzy łącznik jest właścicielem łącznika, skonsolidowanego konta i wszystkie połączone konta.

Przypisywanie uprawnień łącznika do użytkowników po odnalezieniu nie przypisać uprawnienia do istniejących zakresów AWS. Zamiast tego należy tylko nowe, połączone konta mają uprawnienia.

## <a name="additional-steps"></a>Dodatkowe kroki

- [Konfigurowanie grup zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups), jeśli jeszcze go.
- Sprawdź, czy nowe zakresy są dodawane do Twojego selektor zakresu. Nie zapomnij kliknąć **Odśwież** Aby wyświetlić najnowsze dane.
- Na stronie łącznika chmury, wybierz łącznik, a następnie kliknij przycisk **przejdź do konta rozliczeniowego** przypisać połączone konto do grupy zarządzania.

## <a name="manage-cloud-connectors"></a>Zarządzanie łącznikami chmury

Po wybraniu łącznika na stronie łączniki chmury, możesz wykonywać następujące czynności:

- Kliknij przycisk **przejdź do konta rozliczeniowego** Aby wyświetlić informacje o koncie skonsolidowane usługi AWS.
- Kliknij przycisk **kontroli dostępu** Zarządzanie przypisania roli łącznika.
- Kliknij przycisk **Edytuj** można zaktualizować łącznik. Nie można zmienić numer konta usługi AWS, wyświetlaną w ARN roli. Można jednak utworzyć nowy łącznik.
- Kliknij przycisk **Sprawdź** ponownie uruchomić test weryfikacji, aby upewnić się, że Cost Management może zbierać dane przy użyciu ustawień łącznika.

![Przykład przedstawiający listę utworzonych łączników usług AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Rola grupy zarządzania platformy Azure

Do utworzenia jednego miejsca, aby wyświetlić informacje o dostawcy wielu chmur, należy umieścić w tej samej grupie zarządzania do subskrypcji platformy Azure i kont platformy AWS połączone. Jeśli użytkownik jeszcze nie skonfigurowano środowisko platformy Azure przy użyciu grup zarządzania, zobacz [początkowej instalacji grupy zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups).

Jeśli chcesz oddzielić koszty, można utworzyć grupy zarządzania, zawierającą tylko kont platformy AWS, połączone.

## <a name="aws-consolidated-account"></a>Konta usług AWS skonsolidowane

Konta usług AWS skonsolidowane służy do konsolidowania Pomoc dotycząca rozliczeń i płatności dla wielu kont platformy AWS. Konta usług AWS skonsolidowane działa również jako konto połączone usługi AWS.

![Przykład szczegółów konta usługi AWS skonsolidowane](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Ze strony możesz wykonywać następujące czynności:

- Kliknij przycisk **aktualizacji** do aktualizacji zbiorczej połączonych usług AWS skojarzenia kont z grupy zarządzania.
- Kliknij przycisk **kontroli dostępu** można ustawić przypisania roli dla zakresu.

### <a name="aws-consolidated-account-permissions"></a>Skonsolidowane AWS uprawnień konta

Domyślnie AWS skonsolidowane konto, którego uprawnienia zostały ustawione po utworzeniu, w oparciu o uprawnienia łącznika usług AWS. Twórca łącznika jest właścicielem.

Poziom dostępu możesz zarządzać za pomocą strony poziom dostępu do konta usługi AWS skonsolidowane. Jednak uprawnień do usług AWS skonsolidowane konta nie są dziedziczone przez konta usług AWS połączone.

## <a name="aws-linked-account"></a>Usługi AWS połączonego konta usługi

AWS połączonego konta jest, gdzie się do zasobów AWS są tworzone i zarządzane. Połączone konto działa również jako granicy zabezpieczeń.

Na tej stronie możesz wykonywać następujące czynności:

- Kliknij przycisk **aktualizacji** można zaktualizować połączonego AWS skojarzenia konta z grupy zarządzania.
- Kliknij przycisk **kontroli dostępu** można ustawić przypisania roli dla zakresu.

![Przykład strony połączonego konta usługi AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Połączone usługi AWS uprawnień konta

Domyślnie połączonych usług AWS uprawnień konta są ustawiane przy jej tworzeniu, na podstawie uprawnień łącznika usług AWS. Twórca łącznika jest właścicielem. Poziom dostępu są zarządzane za pomocą na stronie poziomu dostępu AWS połączonego konta. Uprawnienia dla konta usługi AWS skonsolidowane nie są dziedziczone przez konta usług AWS połączone.

Konta usługi AWS połączone zawsze dziedziczą uprawnienia z grupy zarządzania, do których należą.

## <a name="next-steps"></a>Kolejne kroki

- Skoro możesz już przygotowana i skonfigurowana AWS kosztów i użycia integracji raportu, przejdź do [koszty zarządzania usługi AWS i użycie](aws-integration-manage.md).
- Jeśli jesteś zaznajomiony z analizy kosztów, zobacz [badanie i analizowanie kosztów za pomocą analizy kosztów](quick-acm-cost-analysis.md) Szybki Start.
- Jeśli znasz budżet na platformie Azure, zobacz [tworzenie i zarządzanie Azure budżetów](tutorial-acm-create-budgets.md) samouczka.
