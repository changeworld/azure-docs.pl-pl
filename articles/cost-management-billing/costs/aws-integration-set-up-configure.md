---
title: Konfigurowanie integracji AWS z usługą Azure Cost Management
description: W tym artykule przedstawiono sposób konfigurowania i konfigurowania integracji raportów o kosztach i użyciu AWS przy użyciu Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 5dbc6ca836c8d1c8b717fd4bf23eab5aa360a288
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988697"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Konfigurowanie i Konfigurowanie integracji raportów o kosztach i użyciu AWS

Dzięki integracji z usługą Amazon Web Services (AWS) i raportem użycia (CUR) można monitorować i kontrolować AWS wydatków w Azure Cost Management. Integracja umożliwia pojedyncze miejsce w Azure Portal, w którym są monitorowane i kontrolowane wydatki na platformę Azure i AWS. W tym artykule wyjaśniono, jak skonfigurować integrację i skonfigurować ją tak, aby można było używać funkcji Azure Cost Management do analizowania kosztów i przeglądania budżetów.

Cost Management przetwarza raport o kosztach i użyciu AWS, który jest przechowywany w zasobniku S3 przy użyciu poświadczeń dostępu AWS w celu pobrania definicji raportu i pobrania plików CSV w formacie GZIP.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Tworzenie raportu o kosztach i użyciu w AWS

Użycie raportu o kosztach i użyciu to zalecany AWS sposób zbierania i przetwarzania kosztów AWS. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [raportów o kosztach i użyciu AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) .

Na stronie **raporty dotyczące użycia & kosztów** w konsoli rozliczeń i Cost Management w programie AWS można utworzyć raport o kosztach i użyciu, wykonując następujące czynności:

1. Zaloguj się do konsoli zarządzania AWS i Otwórz [konsolę rozliczeń i Cost Management](https://console.aws.amazon.com/billing).
2. W okienku nawigacji wybierz kolejno pozycje **koszt & raporty użycia**.
3. Wybierz pozycję **Utwórz raport**.
4. W obszarze **Nazwa raportu**wprowadź nazwę raportu.
5. W obszarze **dodatkowe szczegóły raportu**wybierz pozycję **Dołącz identyfikatory zasobów**.
6. W obszarze **Ustawienia odświeżania danych**wybierz, czy chcesz odświeżyć raport o kosztach i użyciu AWS, jeśli AWS stosuje zwroty, kredyty lub opłaty za pomoc techniczną do konta po sfinalizowaniu rachunku. Po odświeżeniu raportu zostanie przekazany nowy raport do usługi Amazon S3. Zalecamy pozostawienie wybranego ustawienia.
7. Wybierz opcję **Dalej**.
8. W przypadku **zasobnika S3**wybierz pozycję **Konfiguruj**.
9. W oknie dialogowym Konfigurowanie zasobnika S3 wykonaj jedną z następujących czynności:
    1. Wybierz istniejący zasobnik z listy rozwijanej i wybierz pozycję **dalej**.
    2. Wprowadź nazwę zasobnika i region, w którym chcesz utworzyć nowy zasobnik, a następnie wybierz przycisk **dalej**.
10. Zaznacz pole wyboru **potwierdzam, że te zasady są poprawne**, a następnie kliknij przycisk **Zapisz**.
11. Obowiązkowe W polu Prefiks ścieżki raportu wprowadź prefiks ścieżki raportu, który ma zostać poprzedzony nazwą raportu.
Jeśli nie określisz prefiksu, domyślnym prefiksem jest nazwa określona dla raportu. Zakres dat ma format `/report-name/date-range/`.
12. Dla **jednostki czasu**wybierz opcję **co godzinę**.
13. W celu zapewnienia obsługi **wersji raportów**wybierz, czy chcesz, aby każda wersja raportu zastąpiła poprzednią wersję, lub jeśli chcesz uzyskać dodatkowe nowe raporty.
14. W przypadku **włączania integracji danych dla programu**nie jest wymagane zaznaczenie.
15. W obszarze **kompresja**wybierz pozycję **gzip**.
16. Wybierz opcję **Dalej**.
17. Po przejrzeniu ustawień raportu wybierz pozycję **Przegląd i zakończenie**.

    Zanotuj nazwę raportu. Zostanie ona użyta w dalszych krokach.

Rozpoczęcie dostarczania raportów do zasobnika usługi Amazon S3 może potrwać do 24 godzin. Po rozpoczęciu dostarczania program AWS aktualizuje pliki raportów o kosztach i użyciu programu AWS co najmniej raz dziennie. Możesz kontynuować Konfigurowanie środowiska AWS bez oczekiwania na rozpoczęcie dostarczania.

## <a name="create-a-role-and-policy-in-aws"></a>Tworzenie roli i zasad w programie AWS

Azure Cost Management uzyskuje dostęp do zasobnika S3, w którym raport o kosztach i użyciu jest zlokalizowany kilka razy dziennie. Usługa musi mieć dostęp do poświadczeń w celu sprawdzenia, czy są nowe dane. Tworzysz rolę i zasady w programie AWS, aby umożliwić Cost Management dostęp do niej.

Aby włączyć dostęp oparty na rolach do konta AWS w Cost Management, rola zostanie utworzona w konsoli AWS. Musisz mieć _rolę ARN_ i _Identyfikator zewnętrzny_ z konsoli AWS. Później te elementy są używane na stronie **Tworzenie łącznika AWS** w Cost Management.

Użyj Kreatora tworzenia nowej roli:

1. Zaloguj się do konsoli usługi AWS i wybierz pozycję **usługi**.
2. Na liście usług wybierz pozycję **IAM**.
3. Wybierz pozycję **role** , a następnie wybierz pozycję **Utwórz rolę**.
4. Na następnej stronie wybierz **inne konto AWS**.
5. W polu **Identyfikator konta**wprowadź **432263259397**.
6. W obszarze **Opcje**wybierz pozycję **Wymagaj zewnętrznego identyfikatora (najlepszym rozwiązaniem, gdy ta rola zostanie przyjęta przez osobę trzecią)** .
7. W polu **Identyfikator zewnętrzny**wprowadź identyfikator zewnętrzny, który jest udostępnionym kodem dostępu między rolą AWS a Azure Cost Management. Ten sam identyfikator zewnętrzny jest również używany na **nowej stronie łącznika** w Cost Management. Firma Microsoft zaleca użycie silnych zasad kodów dostępu podczas wprowadzania identyfikatora zewnętrznego.

    > [!NOTE]
    > Nie zmieniaj zaznaczenia dla opcji **Wymagaj uwierzytelniania wieloskładnikowego**. Powinien pozostać wyczyszczony.
8. Wybierz pozycję **Dalej: uprawnienia**.
9. Wybierz pozycję **Utwórz zasady**. Zostanie otwarta nowa karta przeglądarki. Jest to miejsce, w którym tworzysz zasady.
10. Wybierz pozycję **Wybierz usługę**.

Skonfiguruj uprawnienia do raportu o kosztach i użyciu:

1. Wprowadź **raport dotyczący kosztów i użycia**.
2. Wybierz pozycję **poziom dostępu** > **Odczyt** > **DescribeReportDefinitions**. Ten krok umożliwia Cost Management odczytywanie, jakie bieżące raporty są zdefiniowane, i ustalanie, czy są one zgodne z wymaganiami wstępnymi definicji raportu.
3. Wybierz pozycję **Dodaj dodatkowe uprawnienia**.

Skonfiguruj uprawnienie do przedziału i obiektów S3:

1. Wybierz pozycję **Wybierz usługę**.
2. Wprowadź **S3**.
3. Wybierz pozycję **poziom dostępu** > **liście** > **ListBucket**. Ta akcja pobiera listę obiektów w zasobniku S3.
4. Wybierz pozycję **poziom dostępu** > **Odczyt** > **GetObject**. Ta akcja umożliwia pobranie plików rozliczeń.
5. Wybierz pozycję **zasoby**.
6. Wybierz **zasobnik — Dodaj ARN**.
7. W polu **Nazwa zasobnika**wprowadź zasobnik używany do przechowywania plików CUR.
8. Wybierz **obiekt — Dodaj ARN**.
9. W polu **Nazwa zasobnika**wprowadź zasobnik używany do przechowywania plików CUR.
10. W polu **Nazwa obiektu**wybierz **dowolny**.
11. Wybierz pozycję **Dodaj dodatkowe uprawnienia**.

Konfigurowanie uprawnień dla Eksploratora kosztów:

1. Wybierz pozycję **Wybierz usługę**.
2. Wprowadź **usługę w Eksploratorze kosztów**.
3. Wybierz pozycję **wszystkie akcje usługi w Eksploratorze kosztów (CE:\*)** . Ta akcja sprawdza, czy kolekcja jest poprawna.
4. Wybierz pozycję **Dodaj dodatkowe uprawnienia**.

Dodaj uprawnienie dla organizacji AWS:

1. Wprowadź **organizację**.
2. Wybierz pozycję **poziom dostępu** > **liście** > **ListAccounts**. Ta akcja pobiera nazwy kont.
3. W obszarze **Przegląd zasad**wprowadź nazwę nowych zasad. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz pozycję **Utwórz zasady**.
4. Wróć do poprzedniej karty i Odśwież stronę sieci Web przeglądarki. Na pasku wyszukiwania Wyszukaj nowe zasady.
5. Wybierz kolejno pozycje **Dalej: przegląd**.
6. Wprowadź nazwę nowej roli. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz pozycję **Utwórz rolę**.

    Zanotuj rolę ARN i identyfikator zewnętrzny użyty w poprzednich krokach podczas tworzenia roli. Będziesz ich używać później podczas konfigurowania łącznika Azure Cost Management.

KOD JSON zasad powinien wyglądać podobnie do poniższego przykładu. Zastąp _wartość nazwą Twojego_ przedziału S3.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Konfigurowanie nowego łącznika AWS na platformie Azure

Skorzystaj z poniższych informacji, aby utworzyć łącznik AWS i rozpocząć monitorowanie kosztów AWS:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Przejdź do **Cost Management i Rozliczeń** > **Cost Management**.
3. W obszarze **Ustawienia**wybierz pozycję **Łączniki chmury (wersja zapoznawcza)** .  
    ![przykład pokazujący ustawienie łączników Cloud (wersja zapoznawcza)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Wybierz pozycję **+ Dodaj** w górnej części strony, aby utworzyć łącznik.
5. Na stronie **Tworzenie łącznika AWS** w polu **Nazwa wyświetlana**wprowadź nazwę łącznika.  
    ![Przykładowa strona do tworzenia łącznika AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Opcjonalnie wybierz domyślną grupę zarządzania. Zostaną zapisane wszystkie wykryte połączone konta. Można skonfigurować go później.
7. W sekcji **rozliczenia** wybierz opcję **automatycznie naliczaj wartość 1%,** Jeśli chcesz zapewnić ciągłą operację po wygaśnięciu wersji zapoznawczej. W przypadku wybrania opcji automatyczna należy wybrać subskrypcję rozliczeń.
8. Dla **roli ARN**wprowadź wartość użytą podczas konfigurowania roli w programie AWS.
9. W polu **Identyfikator zewnętrzny**wprowadź wartość użytą podczas konfigurowania roli w programie AWS.
10. W polu **Nazwa raportu**wprowadź nazwę UTWORZONĄ w AWS.
11. Wybierz pozycję **dalej** , a następnie wybierz pozycję **Utwórz**.

Nowe zakresy AWS mogą potrwać kilka godzin, AWS skonsolidowane konto, AWS połączone konta i ich dane dotyczące kosztów.

Po utworzeniu łącznika Zalecamy przypisanie do niego kontroli dostępu. Użytkownicy mają przypisane uprawnienia do nowo odnalezionych zakresów: AWS skonsolidowane konto i AWS połączone konta. Użytkownik tworzący łącznik to właściciel łącznika, skonsolidowane konto i wszystkie połączone konta.

Przypisanie uprawnień łącznika do użytkowników po wystąpieniu odnajdywania nie powoduje przypisywania uprawnień do istniejących zakresów AWS. Zamiast tego przypisane są tylko nowe połączone konta.

## <a name="take-additional-steps"></a>Wykonaj dodatkowe czynności

- [Skonfiguruj grupy zarządzania](../../governance/management-groups/overview.md#initial-setup-of-management-groups), jeśli jeszcze tego nie zrobiono.
- Sprawdź, czy nowe zakresy są dodawane do selektora zakresu. Wybierz pozycję **Odśwież** , aby wyświetlić najnowsze dane.
- Na stronie **łączniki w chmurze** wybierz swój łącznik i wybierz pozycję **Przejdź do konta rozliczeniowego** , aby przypisać połączone konto do grup zarządzania.

## <a name="manage-cloud-connectors"></a>Zarządzanie łącznikami chmury

Po wybraniu łącznika na stronie **łączniki w chmurze** można wykonać następujące instrukcje:

- Wybierz pozycję **Przejdź do konta rozliczeniowego** , aby wyświetlić informacje dotyczące konta skonsolidowanego AWS.
- Wybierz **Access Control** , aby zarządzać przypisaniem roli dla łącznika.
- Wybierz pozycję **Edytuj** , aby zaktualizować łącznik. Nie można zmienić numeru konta AWS, ponieważ pojawia się on w roli ARN. Można jednak utworzyć nowy łącznik.
- Wybierz pozycję **Weryfikuj** , aby ponownie uruchomić test weryfikacyjny, aby upewnić się, że Cost Management może zbierać dane przy użyciu ustawień łącznika.

![Przykładowa lista utworzonych łączników AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Konfigurowanie grup zarządzania platformy Azure

Umieść subskrypcje platformy Azure i AWS połączone konta w tej samej grupie zarządzania, aby utworzyć pojedynczą lokalizację, w której można wyświetlić informacje o dostawcach międzychmurowych. Jeśli środowisko platformy Azure nie zostało jeszcze skonfigurowane w grupach zarządzania, zobacz [początkowa konfiguracja grup zarządzania](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Jeśli chcesz rozdzielić koszty, możesz utworzyć grupę zarządzania, która zawiera tylko połączone konta AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Skonfiguruj skonsolidowane konto AWS

Skonsolidowane konto AWS łączy rozliczenia i płatność dla wielu kont AWS. Działa również jako połączone konto AWS.

![Przykładowe szczegóły konta skonsolidowanego AWS](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na stronie można:

- Wybierz pozycję **Aktualizuj** , aby zaktualizować zbiorczo skojarzenia AWS połączonych kont z grupą zarządzania.
- Wybierz **Access Control** , aby ustawić przypisanie roli dla zakresu.

### <a name="permissions-for-an-aws-consolidated-account"></a>Uprawnienia dla konta skonsolidowanego AWS

Domyślnie uprawnienia do konta skonsolidowanego AWS są ustawiane podczas tworzenia konta na podstawie uprawnień łącznika AWS. Twórca łącznika jest właścicielem.

Poziom dostępu można zarządzać przy użyciu strony **poziom dostępu** skonsolidowanego konta AWS. Jednak połączone konta AWS nie dziedziczą uprawnień do skonsolidowanego konta AWS.

## <a name="set-up-an-aws-linked-account"></a>Skonfiguruj połączone konto AWS

Połączone konto AWS to miejsce, w którym są tworzone i zarządzane zasoby AWS. Połączone konto działa również jako granica zabezpieczeń.

Na tej stronie można:

- Wybierz pozycję **Aktualizuj** , aby zaktualizować skojarzenie połączonego konta AWS z grupą zarządzania.
- Wybierz **Access Control** , aby ustawić przypisanie roli dla zakresu.

![Przykład strony połączonego konta AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Uprawnienia dla połączonego konta AWS

Domyślnie uprawnienia dla połączonego konta AWS są ustawiane podczas tworzenia na podstawie uprawnień łącznika AWS. Twórca łącznika jest właścicielem. Poziom dostępu można zarządzać przy użyciu strony **poziom dostępu** połączonego konta AWS. Połączone konta AWS nie dziedziczą uprawnień z skonsolidowanego konta AWS.

Połączone konta AWS zawsze dziedziczą uprawnienia z grupy zarządzania, do której należą.

## <a name="next-steps"></a>Następne kroki

- Teraz, po skonfigurowaniu i skonfigurowaniu integracji raportów o kosztach i użyciu AWS, Kontynuuj [Zarządzanie kosztami AWS i użyciem](aws-integration-manage.md).
- Jeśli nie znasz analiz kosztów, zobacz [eksplorowanie i analizowanie kosztów za pomocą analizy kosztów](quick-acm-cost-analysis.md) — Szybki Start.
- Jeśli nie znasz budżetów na platformie Azure, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](tutorial-acm-create-budgets.md).
