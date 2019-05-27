---
title: Instalowanie i konfigurowanie usługi AWS kosztów i użycia integracji raportu z usługi Azure Cost Management
description: W tym artykule opisano Cię przez proces instalowania i konfigurowania usług AWS kosztów i użycia integracji raportu z usługi Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002120"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Instalowanie i konfigurowanie usługi AWS kosztów i użycia integracji raportu

Za pomocą usługi Amazon Web Services (AWS) kosztów i użycia integracji raportu (WALUTA), można monitorować i kontrolować AWS wydatki w usłudze Azure Cost Management. Integracja umożliwia jednej lokalizacji w witrynie Azure portal, gdzie można monitorować i kontroli wydatki na usługi AWS i Azure. W tym artykule opisano sposób konfigurowania integracji i skonfiguruj ją tak, przy użyciu funkcji usługi Azure Cost Management analizując koszty i przejrzyj budżetów.

Koszt procesów zarządzania raportu AWS kosztów i użycia, przechowywane w przedział S3 przy użyciu poświadczeń dostępu AWS, aby uzyskać definicje raportów i Pobierz raport, który pliki GZIP CSV.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Tworzenie raportu kosztów i użycia w usłudze AWS

Za pomocą raportu kosztów i użycia jest zalecane AWS sposobem zbierają i przetwarzają koszty usług AWS. Aby uzyskać więcej informacji, zobacz [koszty usług AWS i raport użycia](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentacji.

Użyj **kosztów i raporty użycia** strony do rozliczeń i zarządzania kosztami konsoli w usłudze AWS, aby utworzyć raport kosztów i użycia wykonując następujące kroki:

1. Zaloguj się do konsoli zarządzania usług AWS, a następnie otwórz [konsoli rozliczeń i zarządzania kosztami](https://console.aws.amazon.com/billing).
2. W okienku nawigacji wybierz **kosztów i raporty użycia**.
3. Wybierz **Utwórz raport**.
4. Aby uzyskać **Nazwa raportu**, wprowadź nazwę dla raportu.
5. Dla **dodatkowe szczegóły** , aby uwzględnić identyfikatory poszczególnych zasobów w raporcie i wybierz **obejmują identyfikatorów zasobów**.
6. Aby uzyskać **ustawienia odświeżania danych**, wybierz, czy raport AWS kosztów i użycia, aby odświeżać AWS stosuje zwroty, środki na korzystanie z, lub obsługuje opłat do swojego konta po finalizowanie rachunku. Gdy raport jest odświeżany, nowy raport zostanie przekazany do Amazon S3. Zalecane jest pozostawienie tego ustawienia włączonego.
7. Wybierz opcję **Dalej**.
8. Aby uzyskać **przedział S3**, wybierz **Konfiguruj**.
9. W oknie dialogowym Konfigurowanie przedział S3 wykonaj jedną z następujących czynności:
    1. Wybierz istniejące przedział z listy rozwijanej i wybierz pozycję **dalej**.
    2. Wprowadź nazwę zasobnika i Region, w którym chcesz utworzyć nowy przedział, a następnie wybierz **dalej**.
10. Wybierz została potwierdzona, te zasady są rozwiązać i wybierz pozycję Zapisz.
11. (Opcjonalnie) Prefiks ścieżki raportu wprowadź prefiksu ścieżki raportu, który ma prepended nazwę raportu.
Jeśli nie określisz prefiks domyślny prefiks jest określona dla raportu w kroku 4 i zakres dat dla raportu, w następującym formacie: `/report-name/date-range/`
12. Aby uzyskać **jednostkę czasu**, wybierz **godzinowe**.
13. Dla **wersji raportu**, wybierz, czy każda wersja raportu zastąpić poprzednią wersję raportu lub został dostarczony poza poprzednich wersji.
14. Aby uzyskać **włączyć integrację danych dla**, zaznaczenie nie jest konieczne.
15. Aby uzyskać **kompresji**, wybierz opcję **GZIP**.
16. Wybierz opcję **Dalej**.
17. Po przejrzeniu ustawienia dla raportu, wybierz **Przejrzyj i Zakończ**.

    Zanotuj nazwę raportu. Zostanie on użyty w kolejnych krokach.

Może upłynąć do 24 godzin usługi AWS rozpocząć dostarczanie raportów do zasobnika usługi Amazon S3. Po rozpoczęciu dostarczania usług AWS aktualizuje pliki raportu usługi AWS kosztów i użycia co najmniej raz dziennie. Możesz kontynuować konfigurowanie środowiska usługi AWS bez oczekiwania na dostarczanie rozpocząć.

## <a name="create-a-role-and-policy-in-aws"></a>Tworzenie ról i zasad w usłudze AWS

Usługa Azure Cost Management uzyskuje dostęp do przedział S3, w którym raport kosztów i użycia znajduje się kilka razy dziennie. Usługa musi mieć dostęp do poświadczeń w celu wyszukiwania nowych danych. Rola i zasady są tworzone w usłudze AWS, aby umożliwić Cost Management, aby uzyskać do niego dostęp.

Aby włączyć opartej na rolach dostęp do konta usługi AWS w Cost Management, rola zostanie utworzona w konsoli usług AWS. Musisz mieć _roli ARN_ i _Identyfikatora zewnętrznego_ za pomocą konsoli usług AWS. Później, użyj na **Tworzenie łącznika usługi AWS** strony w Cost Management.

Użyj Kreatora nowej roli tworzenia:

1. Zaloguj się do konsoli usług AWS i wybierz **usług**.
2. Na liście usług wybierz **IAM**.
3. Wybierz **role** , a następnie wybierz **Utwórz rolę**.
4. Na następnej stronie wybierz **konta usług AWS innego**.
5. W **Accountid**, wprowadź **432263259397**.
6. W **opcje**, wybierz opcję **wymagają Identyfikatora zewnętrznego (najlepsze rozwiązanie, gdy innej założy, ta rola)**.
7. W **Identyfikatora zewnętrznego**, wprowadź identyfikatora zewnętrznego. Identyfikator zewnętrzny jest udostępniony kod dostępu między roli usług AWS i Azure Cost Management. Ten sam identyfikator zewnętrzny jest także używana w **nowy łącznik** strony w Cost Management. Na przykład Identyfikator zewnętrzny przypomina _Companyname1234567890123_.

    > [!NOTE]
    > Nie zmieniaj wybór **wymagają usługi MFA**. Powinny pozostać wyczyszczone.
8. Wybierz opcję **Dalej: Permissions** (Dalej: uprawnienia).
9. Wybierz **Tworzenie zasad**. Zostanie otwarta nowa karta przeglądarki. To, gdzie należy utworzyć zasady.
10. Wybierz **wybierz usługę**.

Skonfiguruj uprawnienia dla raportów kosztów i użycia:

1. Wprowadź **kosztów i raport użycia**.
2. Wybierz **poziom dostępu** > **odczytu** > **DescribeReportDefinitions**. Ten krok umożliwia Cost Management, aby odczytać, jakie raporty bieżące są zdefiniowane i określić, jeśli spełniają warunek wstępny definicji raportu.
3. Wybierz **Dodaj dodatkowe uprawnienia**.

Skonfiguruj uprawnienia dla obiektów i przedział S3:

1. Wybierz **wybierz usługę**.
2. Wprowadź **S3**.
3. Wybierz **poziom dostępu** > **listy** > **ListBucket**. Ta akcja pobiera listę obiektów w przedział S3.
4. Wybierz **poziom dostępu** > **odczytu** > **GetObject**. Ta akcja umożliwia pobieranie plików rozliczeń.
5. Wybierz **zasobów**.
6. Wybierz **zasobnika — Dodaj ARN**.
7. W **Nazwa zasobnika**, wprowadź przedział używane do przechowywania plików WALUTA.
8. Wybierz **obiekt — Dodaj ARN**.
9. W **Nazwa zasobnika**, wprowadź przedział używane do przechowywania plików WALUTA.
10. W **nazwa obiektu**, wybierz opcję **wszelkie**.
11. Wybierz **Dodaj dodatkowe uprawnienia**.

Skonfiguruj uprawnienia dla Eksploratora kosztów:

1. Wybierz **wybierz usługę**.
2. Wprowadź **koszt Explorer z**.
3. Wybierz **akcji w przypadku wszystkich koszt Eksploratora usługi (ce:\*)**. Ta akcja sprawdza, czy kolekcja jest poprawna.
4. Wybierz **Dodaj dodatkowe uprawnienia**.

Dodaj uprawnienia dla organizacji, AWS:

1. Wprowadź **organizacje**.
2. Wybierz **poziom dostępu** > **listy** > **wyświetlanie kont**. Ta akcja pobiera nazwy kont.
3. W **zasady przeglądu**, wprowadź nazwę nowych zasad. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz **Utwórz zasady**.
4. Wróć do poprzedniej karty, a następnie Odśwież w przeglądarce strony sieci Web. Na pasku wyszukiwania należy wyszukiwać nowe zasady.
5. Wybierz **dalej: Przejrzyj**.
6. Wprowadź nazwę dla nowej roli. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz **Utwórz rolę**.

    Uwaga rolę ARN i Identyfikatora zewnętrznego używana w poprzednich krokach, podczas tworzenia roli. Użyjemy ich później podczas konfigurowania łącznika usługi Azure Cost Management.

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

Tworzenie łącznika usługi AWS i rozpocząć monitorowanie koszty usług AWS, należy użyć następujących informacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **Cost Management + rozliczenia** > **Cost Management**.
3. W obszarze **ustawienia**, wybierz opcję **łączników (wersja zapoznawcza) w chmurze**.  
    ![Przykład przedstawiający łączników chmury (wersja zapoznawcza) ustawienie)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Wybierz **+ Dodaj** w górnej części strony Aby utworzyć łącznik.
5. Na **Tworzenie łącznika usługi AWS** stronie **nazwę wyświetlaną**, wprowadź nazwę łącznika.  
    ![Przykład strony do tworzenia łącznika usługi AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcjonalnie wybierz domyślną grupę zarządzania. Wszystkie odnalezione połączone konta zostaną zachowane. Możesz skonfigurować go później.
7. W **rozliczeń** zaznacz **automatycznie opłata w wysokości 1% po ogólnym udostępnieniu** Jeśli chcesz zapewnić ciągłe działanie, po wygaśnięciu wersji zapoznawczej. Jeśli wybierzesz opcję automatycznego, należy wybrać rozliczania subskrypcji.
8. Aby uzyskać **ARN roli**, wprowadź wartość, która została użyta podczas konfigurowania roli w usłudze AWS.
9. Aby uzyskać **Identyfikatora zewnętrznego**, wprowadź wartość, która została użyta podczas konfigurowania roli w usłudze AWS.
10. Aby uzyskać **Nazwa raportu**, wprowadź nazwę, który został utworzony w usłudze AWS.
11. Wybierz **dalej** , a następnie wybierz **Utwórz**.

Może upłynąć kilka godzin w przypadku nowych zakresów usług AWS, AWS skonsolidowane konta i kont platformy AWS połączone i umieścić ich dane kosztów.

Po utworzeniu łącznika, zaleca się przypisać kontroli dostępu do niej. Użytkownicy mają uprawnienia do nowo odnalezione zakresów: Usługi AWS skonsolidowane konta i kont platformy AWS połączone. Użytkownik, który tworzy łącznik jest właścicielem łącznika, skonsolidowanego konta i wszystkie połączone konta.

Przypisywanie uprawnień łącznika do użytkowników po odnalezieniu nie przypisać uprawnienia do istniejących zakresów AWS. Zamiast tego należy tylko nowe, połączone konta mają uprawnienia.

## <a name="take-additional-steps"></a>Wykonanie dodatkowych czynności

- [Konfigurowanie grup zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups), jeśli jeszcze go.
- Sprawdź, czy nowe zakresy są dodawane do Twojego selektor zakresu. Wybierz **Odśwież** Aby wyświetlić najnowsze dane.
- Na **łączników w chmurze** stronie, wybierz swój łącznik i wybierz **przejdź do konta rozliczeniowego** przypisać połączone konto do grupy zarządzania.

## <a name="manage-cloud-connectors"></a>Zarządzanie łącznikami chmury

Po wybraniu łącznika na **łączników w chmurze** strony, możesz:

- Wybierz **przejdź do konta rozliczeniowego** Aby wyświetlić informacje o usłudze AWS skonsolidowanych konta.
- Wybierz **kontroli dostępu** Zarządzanie przypisania roli łącznika.
- Wybierz **Edytuj** można zaktualizować łącznik. Nie można zmienić numer konta usługi AWS, ponieważ znajduje się w roli ARN. Jednak można utworzyć nowy łącznik.
- Wybierz **Sprawdź** ponownie uruchomić test weryfikacji, aby upewnić się, czy Cost Management umożliwia zbieranie danych przy użyciu ustawień łącznika.

![Przykład listy utworzone łączniki usługi AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Konfigurowanie grup zarządzania platformy Azure

Do utworzenia jednego miejsca, aby wyświetlić informacje o dostawcy wielu chmur, należy umieścić w tej samej grupie zarządzania do subskrypcji platformy Azure i kont platformy AWS połączone. Jeśli użytkownik jeszcze nie skonfigurowano środowisko platformy Azure przy użyciu grup zarządzania, zobacz [początkowej instalacji grupy zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups).

Jeśli chcesz oddzielić koszty, można utworzyć grupy zarządzania, zawierającą tylko kont platformy AWS, połączone.

## <a name="set-up-an-aws-consolidated-account"></a>Konfigurowanie usług AWS skonsolidowane konta

Konta usług AWS skonsolidowane łączy Pomoc dotycząca rozliczeń i płatności dla wielu kont platformy AWS. Działa również jako konto usługi AWS połączone.

![Przykład szczegóły dotyczące usług AWS skonsolidowane konta](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na stronie możesz wykonywać następujące czynności:

- Wybierz **aktualizacji** z Zbiorcza aktualizacja skojarzenie AWS połączone konta z grupy zarządzania.
- Wybierz **kontroli dostępu** można ustawić przypisania roli dla zakresu.

### <a name="permissions-for-an-aws-consolidated-account"></a>Uprawnienia do usług AWS skonsolidowane konta

Domyślnie uprawnienia dla konta usługi AWS skonsolidowane są ustawiane podczas tworzenia konta, na podstawie uprawnień łącznika usług AWS. Twórca łącznika jest właścicielem.

Poziom dostępu są zarządzane za pomocą **poziom dostępu** strony AWS skonsolidowane konta. Jednak połączone usługi AWS kont nie dziedziczą uprawnienia do konta usługi AWS skonsolidowane.

## <a name="set-up-an-aws-linked-account"></a>Konfigurowanie konta usługi AWS połączone

Konta usług AWS połączona jest gdzie do zasobów AWS są tworzone i zarządzane. Połączone konto działa również jako granicy zabezpieczeń.

Na tej stronie możesz wykonywać następujące czynności:

- Wybierz **aktualizacji** można zaktualizować skojarzenia usług AWS połączone konta z grupy zarządzania.
- Wybierz **kontroli dostępu** można ustawić przypisania roli dla zakresu.

![Przykład strony połączonego konta usługi AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Uprawnienia dla usług AWS połączone konta

Domyślnie uprawnienia dla konta usługi AWS połączone są ustawiane podczas tworzenia na podstawie uprawnień łącznika usług AWS. Twórca łącznika jest właścicielem. Poziom dostępu są zarządzane za pomocą **poziom dostępu** strony AWS połączone konta. Połączone usługi AWS kont nie dziedziczą uprawnienia z konta usługi AWS skonsolidowane.

Usługi AWS połączone konta zawsze dziedziczą uprawnienia z grupy zarządzania, które należą do.

## <a name="next-steps"></a>Kolejne kroki

- Skoro możesz już przygotowana i skonfigurowana AWS kosztów i użycia integracji raportu, przejdź do [koszty zarządzania usługi AWS i użycie](aws-integration-manage.md).
- Jeśli jesteś zaznajomiony z analizy kosztów, zobacz [badanie i analizowanie kosztów za pomocą analizy kosztów](quick-acm-cost-analysis.md) Szybki Start.
- Jeśli znasz budżet na platformie Azure, zobacz [tworzenie i zarządzanie Azure budżetów](tutorial-acm-create-budgets.md).
