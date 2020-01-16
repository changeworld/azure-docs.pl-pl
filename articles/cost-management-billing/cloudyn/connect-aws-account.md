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
ms.openlocfilehash: b64d54df43b27abb51210995f2426e23690fa2d3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994885"
---
# <a name="connect-an-amazon-web-services-account"></a>Nawiązać połączenie z kontem usług Amazon Web Services

Dostępne są dwie opcje do łączenia z kontem usług Amazon Web Services (AWS) do rozwiązania Cloudyn. Można połączyć z rolą zarządzania tożsamościami i Dostępem, lub przy użyciu konta użytkownika zarządzania tożsamościami i Dostępem tylko do odczytu. Rola zarządzania tożsamościami i Dostępem jest zalecane, ponieważ umożliwia delegowanie dostępu za pomocą określonych uprawnień do zaufane jednostki. Rola zarządzania tożsamościami i Dostępem nie wymaga pozwala na udostępnianie długoterminowe klucze dostępu. Po nawiązaniu połączenia z kontem usług AWS do rozwiązania Cloudyn dane kosztów i użycia są dostępne w raportach usługi Cloudyn. Ten dokument przeprowadzi Cię przez obie opcje.

Aby uzyskać więcej informacji o tożsamościach AWS zarządzania tożsamościami i Dostępem, zobacz [tożsamości (użytkowników, grup i ról)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Również włączyć AWS szczegółowe rozliczeń raporty i informacje przechowywane w Wiadro z usługi AWS simple storage service (S3). Szczegółowe raporty rozliczeń obejmują opłat rozliczeniowych informacjami tagu i zasobów w systemie godzinowym. Przechowywanie raportów umożliwia Cloudyn można pobrać je z Twojej zasobnika i wyświetlić informacje w jego raporty.


## <a name="aws-role-based-access"></a>Usługi AWS dostępu opartej na rolach

Poniższe sekcje prowadzą użytkownika przez proces tworzenia role zarządzania tożsamościami i Dostępem tylko do odczytu, aby zapewnić dostęp do rozwiązania Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Pobieranie Identyfikatora zewnętrznego konta platformy Cloudyn

Pierwszym krokiem jest uzyskanie hasła unikatowego połączenia w portalu Cloudyn. Jest używana w usłudze AWS, jako **Identyfikatora zewnętrznego**.

1. Otwórz Cloudyn portal z witryny Azure portal lub przejdź do [ https://azure.cloudyn.com ](https://azure.cloudyn.com) i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **kont w chmurze**.
3. Zarządzanie kontami, wybierz **kont platformy AWS** kartę, a następnie kliknij przycisk **Dodaj nowe +** .
4. W **Dodawanie konta usługi AWS** okno dialogowe, kopia **Identyfikatora zewnętrznego** i Zapisz wartość dla roli usługi AWS tworzenia kroki opisane w następnej sekcji. Identyfikator zewnętrzny jest unikatowy dla swojego konta. Przykład Identyfikator zewnętrzny jest — na poniższej ilustracji _Contoso_ następuje numer. Twój identyfikator różni się.  
    ![Identyfikator zewnętrzny wyświetlane w oknie Dodaj konto usług AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu opartej na rolach

1. Zaloguj się do konsoli usług AWS na https://console.aws.amazon.com/iam/home i wybierz **role**.
2. Kliknij przycisk **Utwórz rolę** , a następnie wybierz **konta usług AWS innego**.
3. W **Accountid** pole, Wklej `432263259397`. Ten identyfikator konta jest konto modułu zbierającego dane Cloudyn przypisane w usługach AWS z usługą Cloudyn. Użyj dokładnie pokazany identyfikator konta.
4. Obok pozycji **opcje**, wybierz opcję **wymagają Identyfikatora zewnętrznego**. Wklej Twoja unikatowa wartość, która wcześniej skopiowany z **Identyfikatora zewnętrznego** pola w rozwiązaniu Cloudyn. Następnie kliknij przycisk **dalej: uprawnienia**.  
    ![Wklej Identyfikator zewnętrzny z platformy Cloudyn, na stronie Tworzenie roli](./media/connect-aws-account/create-role01.png)
5. W obszarze **Dołącz zasady uprawnień**w **typ zasad** filtru pole wyszukiwania, typ `ReadOnlyAccess`, wybierz opcję **ReadOnlyAccess**, następnie kliknij przycisk **dalej: Przegląd**.  
    ![Wybierz tylko do odczytu na liście nazwy zasad](./media/connect-aws-account/readonlyaccess.png)
6. Na stronie Przegląd upewnij się, wybrane opcje są poprawne i wpisz **nazwy roli**. Na przykład *usługa Azure-Cost-zarz*. Wprowadź **Opis roli**. Na przykład _przypisania roli na platformie Cloudyn_, następnie kliknij przycisk **tworzenia ról**.
7. W **role** listy, kliknij rolę został utworzony i skopiuj **ARN roli** wartości na stronie podsumowania. Użyj wartości ARN roli (nazwa zasobu Amazon) później, po zarejestrowaniu konfigurację w rozwiązaniu Cloudyn.  
    ![Skopiuj ARN roli na stronie podsumowania](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Skonfigurowanie dostępu roli usług AWS zarządzania tożsamościami i Dostępem w rozwiązaniu Cloudyn

1. Otwórz Cloudyn portal z witryny Azure portal lub przejdź do https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **kont w chmurze**.
3. Zarządzanie kontami, wybierz **kont platformy AWS** kartę, a następnie kliknij przycisk **Dodaj nowe +** .
4. W **nazwa konta**, wpisz nazwę konta.
5. Obok pozycji **typ dostępu**, wybierz opcję **Rola zarządzania tożsamościami i Dostępem**.
6. W **ARN roli** pole, Wklej wartość skopiowany wcześniej, a następnie kliknij przycisk **Zapisz**.  
    ![Wklej ARN roli okno Dodawanie konta usługi AWS](./media/connect-aws-account/add-aws-account-box.png)


Konta usługi AWS pojawia się na liście kont. **Identyfikator właściciela** wymienione dopasowuje wartość ARN roli. Twoje **stan konta** powinny mieć symbol zielony znacznik wyboru wskazujący, że Cloudyn można uzyskać dostępu do konta usługi AWS. Do momentu włączenia szczegółowe rozliczeń usługi AWS swój status konsolidacji pojawia się jako **autonomiczny**.

![Stan konta usługi AWS wyświetlany na stronie Zarządzanie kontami](./media/connect-aws-account/aws-account-status01.png)

Cloudyn rozpoczyna się zbieranie danych i wypełniania raportów. Następnie [Włączanie szczegółowe rozliczeń usługi AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Dostęp oparty na protokole użytkownika usług AWS

Poniższe sekcje prowadzą użytkownika przez proces tworzenia użytkownika tylko do odczytu w celu zapewnienia dostępu do rozwiązania Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu oparte na użytkownikach

1. Zaloguj się do konsoli usług AWS na https://console.aws.amazon.com/iam/home i wybierz **użytkowników**.
2. Kliknij pozycję **Add User** (Dodaj użytkownika).
3. W **nazwa_użytkownika** wpisz nazwę użytkownika.
4. Dla **dostęp typu**, wybierz opcję **dostęp programowy** i kliknij przycisk **dalej: uprawnienia**.  
    ![Wprowadź nazwę użytkownika na stronie Dodawanie użytkownika](./media/connect-aws-account/add-user01.png)
5. Uprawnienia, można wybrać **Dołącz istniejące zasady bezpośrednio**.
6. W obszarze **Dołącz zasady uprawnień**w **typ zasad** filtru pole wyszukiwania, typ `ReadOnlyAccess`, wybierz opcję **ReadOnlyAccess**, a następnie kliknij przycisk **dalej : Przejrzyj**.  
    ![Wybierz ReadOnlyAccess, aby ustawić uprawnienia dla użytkownika](./media/connect-aws-account/set-permission-for-user.png)
7. Na stronie Przegląd upewnij się, wybrane opcje są poprawne, a następnie kliknij przycisk **Utwórz użytkownika**.
8. Na stronie pełny dostęp do kluczowych identyfikator i klucz tajny klucz dostępu są wyświetlane. Te informacje umożliwiają konfigurowanie rejestracji w rozwiązaniu Cloudyn.
9. Kliknij przycisk **Pobierz CSV** i Zapisz plik credentials.csv w bezpiecznej lokalizacji.  
    ![Kliknij przycisk pobierania CSV, aby zapisać poświadczenia](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurowanie dostępu oparte na użytkownikach zarządzanie dostępem i Tożsamościami w usłudze AWS w rozwiązaniu Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **kont w chmurze**.
3. Zarządzanie kontami, wybierz **kont platformy AWS** kartę, a następnie kliknij przycisk **Dodaj nowe +** .
4. Aby uzyskać **nazwa konta**, wpisz nazwę konta.
5. Obok pozycji **typ dostępu**, wybierz opcję **użytkownika zarządzania tożsamościami i Dostępem**.
6. W **klucz dostępu**, Wklej **dostępu Identyfikatora klucza** wartości z pliku credentials.csv.
7. W **klucz tajny**, Wklej **klucz dostępu do kluczy tajnych** wartości z pliku credentials.csv, a następnie kliknij przycisk **Zapisz**.  

Konta usługi AWS pojawia się na liście kont. Twoje **stan konta** powinny mieć zielony znacznik wyboru.

Cloudyn rozpoczyna się zbieranie danych i wypełniania raportów. Następnie [Włączanie szczegółowe rozliczeń usługi AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Włączanie szczegółowe rozliczeń usługi AWS

Wykonaj następujące kroki, można pobrać ARN roli usługi AWS. ARN roli umożliwia przyznawanie uprawnień do odczytu do zasobnika rozliczeń.

1. Zaloguj się do konsoli usług AWS na https://console.aws.amazon.com i wybierz **usług**.
2. W polu wyszukiwania usługi pole typu *IAM*i wybierz tę opcję.
3. Wybierz **role** z menu po lewej stronie.
4. Na liście ról wybierz rolę którą utworzono uzyskać dostęp do rozwiązania Cloudyn.
5. Na stronie Podsumowanie ról kliknij, aby skopiować **ARN roli**. Zachowaj ARN roli jest przydatne do wykonania kolejnych kroków.

### <a name="create-an-s3-bucket"></a>Utwórz przedział S3

Możesz utworzyć przedział S3 do przechowywania szczegółowych informacji dotyczących rozliczeń.

1. Zaloguj się do konsoli usług AWS na https://console.aws.amazon.com i wybierz **usług**.
2. W polu wyszukiwania usługi pole typu *S3*i wybierz **S3**.
3. Na stronie Amazon S3 kliknij **zasobnika Utwórz**.
4. W kreatorze tworzenia zasobnika Wybierz przedział nazwę i Region, a następnie kliknij przycisk **dalej**.  
    ![Przykładowe informacje, jeden, Utwórz stronę zasobnika](./media/connect-aws-account/create-bucket.png)
5. Na **Ustaw właściwości** strony, Zachowaj wartości domyślne, a następnie kliknij przycisk **dalej**.
6. Na stronie Przegląd kliknij **zasobnika Utwórz**. Jest wyświetlana na liście przedziału.
7. Kliknij przedział, który został utworzony, a następnie wybierz pozycję **uprawnienia** , a następnie wybierz pozycję **zasad zasobnika**. Zostanie otwarty Edytor zasad przedziału.
8. Skopiuj poniższy przykład kodu JSON i wklej go w edytorze zasad przedziału.
   - Zastąp `<BillingBucketName>` nazwą Twojego przedział S3.
   - Zastąp `<ReadOnlyUserOrRole>` z rolą lub ARN użytkownika, który skopiowany wcześniej.

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
    ![Kliknij przycisk Zapisz w edytorze zasad zasobnika](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Włączanie rozliczeń raporty usług AWS

Po utworzeniu i skonfigurowaniu przedział S3, przejdź do [rozliczeń preferencje](https://console.aws.amazon.com/billing/home?#/preference) w konsoli usług AWS.

1. Na stronie preferencji wybierz **otrzymywać raporty rozliczeń**.
2. W obszarze **otrzymywać raporty rozliczeń**, wprowadź nazwę pakietu, który został utworzony, a następnie kliknij przycisk **Sprawdź**.  
3. Wybierz wszystkie cztery opcje poziom szczegółowości raportów, a następnie kliknij przycisk **Zapisz preferencje**.  
    ![Wybierz poziom szczegółowości, aby włączyć raporty](./media/connect-aws-account/enable-reports.png)

Cloudyn pobiera szczegółowe informacje rozliczeniowe z Twojej przedział S3 i wypełnia raportów, po włączeniu szczegółowe rozliczeń. Może upłynąć do 24 godzin, dopóki szczegółowych danych dotyczących rozliczeń jest wyświetlana w konsoli usługi Cloudyn. Po udostępnieniu szczegółowych danych dotyczących rozliczeń stanie konsolidacji konta pojawi się jako **skonsolidowanych**. Stan konta zostanie wyświetlony jako **Ukończono**.

![Stan konsolidacji pokazywanymi na karcie kont platformy AWS](./media/connect-aws-account/consolidated-status.png)

Niektóre raporty optymalizacji mogą wymagać kilku dni dane, aby uzyskać rozmiar próbki danych odpowiednich dla dokładnego zalecenia.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat platformy Cloudyn, przejdź do [przeglądanie użycia i kosztów](tutorial-review-usage.md) samouczek dotyczący platformy Cloudyn.
