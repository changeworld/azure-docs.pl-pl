---
title: Łączenie konta usługi Amazon Web Services do rozwiązania Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: Połącz z kontem usług Amazon Web Services do wyświetlania danych kosztów i użycia w raportach usługi Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219741"
---
# <a name="connect-an-amazon-web-services-account"></a>Nawiązać połączenie z kontem usług Amazon Web Services

Dostępne są dwie opcje do łączenia z kontem usług Amazon Web Services (AWS) do rozwiązania Cloudyn. Można połączyć z rolą zarządzania tożsamościami i Dostępem, lub przy użyciu konta użytkownika zarządzania tożsamościami i Dostępem tylko do odczytu. Rola zarządzania tożsamościami i Dostępem jest zalecane, ponieważ umożliwia delegowanie dostępu za pomocą określonych uprawnień do zaufane jednostki. Rola zarządzania tożsamościami i Dostępem nie wymaga pozwala na udostępnianie długoterminowe klucze dostępu. Po nawiązaniu połączenia z kontem usług AWS do rozwiązania Cloudyn dane kosztów i użycia są dostępne w raportach usługi Cloudyn. Ten dokument przeprowadzi Cię przez obie opcje.

Aby uzyskać więcej informacji na temat tożsamości usługi AWS w usłudze IAM, zobacz [tożsamości (Użytkownicy, grupy i role)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Również włączyć AWS szczegółowe rozliczeń raporty i informacje przechowywane w Wiadro z usługi AWS simple storage service (S3). Szczegółowe raporty rozliczeń obejmują opłat rozliczeniowych informacjami tagu i zasobów w systemie godzinowym. Przechowywanie raportów umożliwia Cloudyn można pobrać je z Twojej zasobnika i wyświetlić informacje w jego raporty.


## <a name="aws-role-based-access"></a>Usługi AWS dostępu opartej na rolach

Poniższe sekcje prowadzą użytkownika przez proces tworzenia role zarządzania tożsamościami i Dostępem tylko do odczytu, aby zapewnić dostęp do rozwiązania Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Pobieranie Identyfikatora zewnętrznego konta platformy Cloudyn

Pierwszym krokiem jest uzyskanie hasła unikatowego połączenia w portalu Cloudyn. Jest on używany w AWS jako **Identyfikator zewnętrzny**.

1. Otwórz Portal Cloudyn w Azure Portal lub przejdź do [https://azure.cloudyn.com](https://azure.cloudyn.com) i zaloguj się.
2. Kliknij symbol koło zębate, a następnie wybierz pozycję **konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **konta AWS** , a następnie kliknij pozycję **Dodaj nowe +** .
4. W oknie dialogowym **Dodawanie konta AWS** Skopiuj **Identyfikator zewnętrzny** i Zapisz wartość dla kroków tworzenia roli AWS w następnej sekcji. Identyfikator zewnętrzny jest unikatowy dla swojego konta. Na poniższej ilustracji przykład zewnętrzny identyfikator to _contoso_ , po którym następuje cyfra. Twój identyfikator różni się.  
    ![identyfikator zewnętrzny widoczny w polu Dodaj konto AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu opartej na rolach

1. Zaloguj się do konsoli AWS w https://console.aws.amazon.com/iam/home i wybierz pozycję **role**.
2. Kliknij pozycję **Utwórz rolę** , a następnie wybierz **inne konto AWS**.
3. W polu **Identyfikator konta** wklej `432263259397`. Ten identyfikator konta jest konto modułu zbierającego dane Cloudyn przypisane w usługach AWS z usługą Cloudyn. Użyj dokładnie pokazany identyfikator konta.
4. Obok **opcji Opcje**wybierz pozycję **Wymagaj identyfikatora zewnętrznego**. Wklej unikatową wartość, która wcześniej była kopiowana z pola **Identyfikator zewnętrzny** w Cloudyn. Następnie kliknij przycisk **Dalej: uprawnienia**.  
    ![wklejanie zewnętrznego identyfikatora z Cloudyn na stronie Tworzenie roli](./media/connect-aws-account/create-role01.png)
5. W obszarze **Dołącz uprawnienia zasady**w polu Filtr **typu zasad** wpisz `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, a następnie kliknij przycisk **Dalej: przegląd**.  
    ![wybierz pozycję dostęp tylko do odczytu na liście nazw zasad](./media/connect-aws-account/readonlyaccess.png)
6. Na stronie Recenzja upewnij się, że wybrane opcje są poprawne, a następnie wpisz **nazwę roli**. Na przykład *usługa Azure-Cost-zarz*. Wprowadź **Opis roli**. Na przykład _przypisanie roli dla Cloudyn_, a następnie kliknij pozycję **Utwórz rolę**.
7. Na liście **role** kliknij utworzoną rolę i skopiuj wartość **ARN roli** ze strony Podsumowanie. Użyj wartości ARN roli (nazwa zasobu Amazon) później, po zarejestrowaniu konfigurację w rozwiązaniu Cloudyn.  
    ![skopiować rolę ARN ze strony Podsumowanie](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Skonfigurowanie dostępu roli usług AWS zarządzania tożsamościami i Dostępem w rozwiązaniu Cloudyn

1. Otwórz Portal Cloudyn w Azure Portal lub przejdź do https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koło zębate, a następnie wybierz pozycję **konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **konta AWS** , a następnie kliknij pozycję **Dodaj nowe +** .
4. W polu **nazwa konta**wpisz nazwę konta.
5. Obok pozycji **Typ dostępu**wybierz pozycję **rola IAM**.
6. W polu **ARN roli** wklej wcześniej skopiowaną wartość, a następnie kliknij przycisk **Zapisz**.  
    ![wkleić ARN roli w polu Dodaj konto AWS](./media/connect-aws-account/add-aws-account-box.png)


Konta usługi AWS pojawia się na liście kont. **Identyfikator właściciela** jest zgodny z wartością ARN roli. **Stan konta** powinien mieć zielony symbol znacznika wyboru wskazujący, że Cloudyn może uzyskać dostęp do konta AWS. Do momentu włączenia szczegółowych rozliczeń AWS stan konsolidacji zostanie wyświetlony jako **samodzielny**.

![Stan konta usługi AWS wyświetlany na stronie Zarządzanie kontami](./media/connect-aws-account/aws-account-status01.png)

Cloudyn rozpoczyna się zbieranie danych i wypełniania raportów. Następnie [Włącz szczegółowe rozliczanie AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Dostęp oparty na protokole użytkownika usług AWS

Poniższe sekcje prowadzą użytkownika przez proces tworzenia użytkownika tylko do odczytu w celu zapewnienia dostępu do rozwiązania Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu oparte na użytkownikach

1. Zaloguj się do konsoli AWS w https://console.aws.amazon.com/iam/home i wybierz pozycję **Użytkownicy**.
2. Kliknij pozycję **Add User** (Dodaj użytkownika).
3. W polu **Nazwa użytkownika** wpisz nazwę użytkownika.
4. W **polu Typ dostępu**wybierz pozycję **dostęp programistyczny** i kliknij przycisk **Dalej: uprawnienia**.  
    ![wprowadź nazwę użytkownika na stronie Dodawanie użytkownika](./media/connect-aws-account/add-user01.png)
5. W obszarze Uprawnienia wybierz pozycję **Dołącz istniejące zasady bezpośrednio**.
6. W obszarze **Dołącz uprawnienia zasady**w polu Filtr **typu zasad** wpisz `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, a następnie kliknij przycisk **Dalej: przegląd**.  
    ![wybrać ReadOnlyAccess, aby ustawić uprawnienia dla użytkownika](./media/connect-aws-account/set-permission-for-user.png)
7. Na stronie Recenzja upewnij się, że wybrane opcje są poprawne, a następnie kliknij pozycję **Utwórz użytkownika**.
8. Na stronie pełny dostęp do kluczowych identyfikator i klucz tajny klucz dostępu są wyświetlane. Te informacje umożliwiają konfigurowanie rejestracji w rozwiązaniu Cloudyn.
9. Kliknij pozycję **Pobierz. csv** i Zapisz plik. CSV z poświadczeniami w bezpiecznej lokalizacji.  
    ![kliknij pozycję Pobierz plik CSV, aby zapisać poświadczenia](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurowanie dostępu oparte na użytkownikach zarządzanie dostępem i Tożsamościami w usłudze AWS w rozwiązaniu Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koło zębate, a następnie wybierz pozycję **konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **konta AWS** , a następnie kliknij pozycję **Dodaj nowe +** .
4. W obszarze **nazwa konta**wpisz nazwę konta.
5. Obok pozycji **Typ dostępu**wybierz pozycję **użytkownik**w usłudze IAM.
6. W polu **klucz dostępu**wklej wartość **Identyfikator klucza dostępu** z pliku Credentials. csv.
7. W **kluczu tajnym**wklej wartość **klucza dostępu tajnego** z pliku Credentials. csv, a następnie kliknij przycisk **Zapisz**.  

Konta usługi AWS pojawia się na liście kont. **Stan konta** powinien mieć zielony symbol znacznika wyboru.

Cloudyn rozpoczyna się zbieranie danych i wypełniania raportów. Następnie [Włącz szczegółowe rozliczanie AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Włączanie szczegółowe rozliczeń usługi AWS

Wykonaj następujące kroki, można pobrać ARN roli usługi AWS. ARN roli umożliwia przyznawanie uprawnień do odczytu do zasobnika rozliczeń.

1. Zaloguj się do konsoli usługi AWS w https://console.aws.amazon.com i wybierz pozycję **usługi**.
2. W polu wyszukiwania usług wpisz *IAM*i wybierz tę opcję.
3. Z menu po lewej stronie wybierz pozycję **role** .
4. Na liście ról wybierz rolę którą utworzono uzyskać dostęp do rozwiązania Cloudyn.
5. Na stronie Podsumowanie ról kliknij, aby skopiować **rolę ARN**. Zachowaj ARN roli jest przydatne do wykonania kolejnych kroków.

### <a name="create-an-s3-bucket"></a>Utwórz przedział S3

Możesz utworzyć przedział S3 do przechowywania szczegółowych informacji dotyczących rozliczeń.

1. Zaloguj się do konsoli usługi AWS w https://console.aws.amazon.com i wybierz pozycję **usługi**.
2. W polu wyszukiwania usługi wpisz *S3*, a następnie wybierz pozycję **S3**.
3. Na stronie Amazon S3 kliknij pozycję **Utwórz zasobnik**.
4. W Kreatorze tworzenia zasobnika wybierz nazwę i region zasobnika, a następnie kliknij przycisk **dalej**.  
    ![przykładowe informacje na stronie Tworzenie zasobnika](./media/connect-aws-account/create-bucket.png)
5. Na stronie **Ustawianie właściwości** Zachowaj wartości domyślne, a następnie kliknij przycisk **dalej**.
6. Na stronie Przegląd kliknij pozycję **Utwórz zasobnik**. Jest wyświetlana na liście przedziału.
7. Kliknij utworzony zasobnik i wybierz kartę **uprawnienia** , a następnie wybierz pozycję **zasady**przedziału. Zostanie otwarty Edytor zasad przedziału.
8. Skopiuj poniższy przykład kodu JSON i wklej go w edytorze zasad przedziału.
   - Zastąp `<BillingBucketName>` nazwą Twojego przedziału S3.
   - Zastąp `<ReadOnlyUserOrRole>` rolą lub ARN użytkownika, które zostały wcześniej skopiowane.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Kliknij pozycję **Zapisz**.  
    ![kliknij pozycję Zapisz w edytorze zasad zasobników](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Włączanie rozliczeń raporty usług AWS

Po utworzeniu i skonfigurowaniu zasobnika S3 przejdź do [preferencji rozliczeń](https://console.aws.amazon.com/billing/home?#/preference) w konsoli AWS.

1. Na stronie Preferencje wybierz pozycję **Odbierz raporty dotyczące rozliczeń**.
2. W obszarze **odbieranie raportów rozliczeń**wprowadź nazwę utworzonego zasobnika, a następnie kliknij przycisk **Weryfikuj**.  
3. Wybierz wszystkie cztery opcje szczegółowości raportu, a następnie kliknij przycisk **Zapisz preferencje**.  
    ![Wybieranie stopnia szczegółowości w celu włączenia raportów](./media/connect-aws-account/enable-reports.png)

Cloudyn pobiera szczegółowe informacje rozliczeniowe z Twojej przedział S3 i wypełnia raportów, po włączeniu szczegółowe rozliczeń. Może upłynąć do 24 godzin, dopóki szczegółowych danych dotyczących rozliczeń jest wyświetlana w konsoli usługi Cloudyn. Gdy dostępne są szczegółowe dane dotyczące rozliczeń, stan konsolidacji konta zostanie wyświetlony jako **skonsolidowany**. Stan konta jest wyświetlany jako **zakończony**.

![Stan konsolidacji pokazywanymi na karcie kont platformy AWS](./media/connect-aws-account/consolidated-status.png)

Niektóre raporty optymalizacji mogą wymagać kilku dni dane, aby uzyskać rozmiar próbki danych odpowiednich dla dokładnego zalecenia.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat Cloudyn, przejdź do samouczka [Przegląd użycia i kosztów](tutorial-review-usage.md) dla Cloudyn.
