---
title: Konta usług Amazon Web Services połączyć z usługą Azure koszt Management | Dokumentacja firmy Microsoft
description: Połącz konta usług Amazon Web Services do wyświetlania kosztów i użycia danych w raportach dotyczących zarządzania kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0c18fc065ae4d9a9401a8d603f051e9d6236c538
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Połącz konta usług Amazon Web Services

Dostępne są dwie opcje do nawiązania połączenia Azure koszt zarządzania kontem usługi Amazon Web Services (AWS). Można połączyć z rolą IAM lub na koncie użytkownika IAM tylko do odczytu. Rola IAM jest zalecane, ponieważ pozwala na delegowany dostęp z uprawnieniami zdefiniowanych zaufane jednostki. Rola IAM nie wymaga udziału długoterminowe klucze dostępu. Po nawiązaniu połączenia konta usług AWS koszt zarządzania, kosztów i użycia danych jest dostępne w raportach kosztów zarządzania. Ten dokument przeprowadzi Cię przez obie opcje.

Aby uzyskać więcej informacji o tożsamości usług AWS IAM, zobacz [tożsamości (użytkowników, grup i ról)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Również włączyć AWS szczegółowe raporty dotyczące rozliczeń i przechowywania informacji w zasobniku usług AWS proste magazynu usługi (S3). Szczegółowe raporty rozliczeń obejmują rozliczeń opłat informacji tagu i zasobów na godzinę. Zapisywanie raportów umożliwia zarządzanie koszt można pobrać je z Twojej zasobnika i wyświetlić te informacje w jego raporty.


## <a name="aws-role-based-access"></a>Dostęp do usług AWS opartej na rolach

Poniższe sekcje przeprowadzenie tworzenie roli IAM tylko do odczytu do zapewnienia dostępu do zarządzania kosztów.

### <a name="get-your-cost-management-account-external-id"></a>Pobierz identyfikator zewnętrznego konta kosztów zarządzania

Pierwszym krokiem jest uzyskanie hasła unikatowe połączenia z portalu Azure kosztów zarządzania. Jest on używany w AWS jako **Identyfikatora zewnętrznego**.

1. Otwórz Cloudyn portal z portalu Azure lub przejdź do [ https://azure.cloudyn.com ](https://azure.cloudyn.com) i zaloguj się.
2. Kliknij koło zębate symbol, a następnie wybierz **kont chmury**.
3. Zarządzanie kontami wybierz **kont usług AWS** a następnie kliknij pozycję **Dodaj nowy +**.
4. W **Dodaj konto usług AWS** okno dialogowe, kopiowania **Identyfikatora zewnętrznego** i Zapisz wartość dla roli usług AWS kroki tworzenia w następnej sekcji. Zewnętrzne identyfikator jest unikatowy do Twojego konta. Przykład identyfikatorów zewnętrznych jest — na poniższej ilustracji, _Contoso_ z numerem. Inny identyfikator.  
    ![Identyfikator zewnętrznego](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu opartej na rolach

1. Zaloguj się do konsoli usług AWS pod adresem https://console.aws.amazon.com/iam/home i wybierz **ról**.
2. Kliknij przycisk **Utwórz rolę** , a następnie wybierz **AWS innego konta**.
3. W **Identyfikatora** Wklej `432263259397`. Ten identyfikator konta jest konto moduł zbierający dane kosztów zarządzania przypisany przez usług AWS z usługą Cloudyn. Użyć dokładnego Identyfikatora konta wyświetlane.
4. Obok pozycji **opcje**, wybierz pozycję **wymagają Identyfikatora zewnętrznego**. Wklej z unikatową wartość, która wcześniej skopiowany z **Identyfikatora zewnętrznego** w kosztów zarządzania. Następnie kliknij przycisk **dalej: uprawnienia**.  
    ![Tworzenie roli](./media/connect-aws-account/create-role01.png)
5. W obszarze **Dołącz zasady uprawnień**w **typ zasad** filtru pole wyszukiwania, wpisz `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, następnie kliknij przycisk **dalej: Przegląd**.  
    ![Dostęp tylko do odczytu](./media/connect-aws-account/readonlyaccess.png)
6. Na stronie Przegląd Sprawdź wybrane opcje są poprawne i wpisz **nazwy roli**. Na przykład *Mgt-Azure-koszt*. Wprowadź **opis roli**. Na przykład _przypisania roli dla usługi Azure Management koszt_, następnie kliknij przycisk **Utwórz rolę**.
7. W **ról** , kliknij utworzoną rolę, a następnie skopiuj **ARN roli** wartości na stronie podsumowania. Użyj wartości ARN roli (nazwa zasobu usługi Amazon) później podczas rejestrowania konfigurację w Azure kosztów zarządzania.  
    ![Rola ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Skonfigurowanie dostępu roli usług AWS IAM w koszt zarządzania

1. Otwórz Cloudyn portal z portalu Azure lub przejdź do https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij koło zębate symbol, a następnie wybierz **kont chmury**.
3. Zarządzanie kontami wybierz **kont usług AWS** a następnie kliknij pozycję **Dodaj nowy +**.
4. W **nazwa konta**, wpisz nazwę konta.
5. Obok pozycji **typu dostępu**, wybierz pozycję **roli IAM**.
6. W **ARN roli** pól, wklej skopiowane wcześniej wartość, a następnie kliknij przycisk **zapisać**.  
    ![Dodaj konto usług AWS — okno](./media/connect-aws-account/add-aws-account-box.png)


Konta usług AWS pojawią się na liście kont. **Identyfikator właściciela** wymienione jest zgodna z wartością ARN roli. Twoje **stan konta** powinien mieć wskazujący, że koszt zarządzania można uzyskać dostępu do konta usług AWS symbol zielony znacznik wyboru. Przed włączeniem szczegółowe rozliczeń usług AWS stan konsolidacji jest wyświetlany jako **autonomiczny**.

![Stan konta usług AWS](./media/connect-aws-account/aws-account-status01.png)

Kosztów zarządzania rozpoczyna zbieranie danych i wypełniania raportów. Następnie [Włącz szczegółowe rozliczeń usług AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Dostęp do usług AWS oparta na użytkowniku

Poniższe sekcje przeprowadzenie Tworzenie użytkownika tylko do odczytu w celu zapewnienia dostępu do zarządzania koszt.

### <a name="add-aws-read-only-user-based-access"></a>Dodawanie usług AWS dostęp tylko do odczytu oparta na użytkowniku

1. Zaloguj się do konsoli usług AWS pod adresem https://console.aws.amazon.com/iam/home i wybierz **użytkowników**.
2. Kliknij przycisk **dodać użytkownika**.
3. W **nazwy użytkownika** wpisz nazwę użytkownika.
4. Dla **dostęp typu**, wybierz pozycję **dostęp programistyczny** i kliknij przycisk **dalej: uprawnienia**.  
    ![Dodaj użytkownika](./media/connect-aws-account/add-user01.png)
5. Uprawnienia, wybierz **dołączyć istniejących zasad bezpośrednio**.
6. W obszarze **Dołącz zasady uprawnień**w **typ zasad** filtru pole wyszukiwania, wpisz `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, a następnie kliknij przycisk **dalej : Przejrzyj**.  
    ![Ustawianie uprawnień dla użytkownika](./media/connect-aws-account/set-permission-for-user.png)
7. Na stronie przeglądu, sprawdź wybrane opcje są poprawne, a następnie kliknij przycisk **tworzenia użytkownika**.
8. Na stronie pełny dostęp do klucza identyfikator i klucz tajny klucz dostępu do są wyświetlane. Te informacje umożliwiają konfigurowanie rejestracji w kosztów zarządzania.
9. Kliknij przycisk **Pobierz CSV** i Zapisz plik credentials.csv do bezpiecznej lokalizacji.  
    ![Pobierz poświadczenia](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Konfigurowanie dostępu oparte na użytkowniku usług AWS IAM w koszt zarządzania

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij koło zębate symbol, a następnie wybierz **kont chmury**.
3. Zarządzanie kontami wybierz **kont usług AWS** a następnie kliknij pozycję **Dodaj nowy +**.
4. Aby uzyskać **nazwa konta**, wpisz nazwę konta.
5. Obok pozycji **typu dostępu**, wybierz pozycję **użytkownika IAM**.
6. W **klucz dostępu**, Wklej **dostępu Identyfikatora klucza** wartości z pliku credentials.csv.
7. W **klucz tajny**, Wklej **klucza tajnego dostępu** wartości z pliku credentials.csv, a następnie kliknij przycisk **zapisać**.  

Konta usług AWS pojawią się na liście kont. Twoje **stan konta** powinien mieć zielony znacznik wyboru.

Kosztów zarządzania rozpoczyna zbieranie danych i wypełniania raportów. Następnie [Włącz szczegółowe rozliczeń usług AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Włącz szczegółowe usług AWS rozliczeń

Można pobrać z usług AWS ARN roli, wykonaj następujące kroki. ARN roli umożliwia przyznanie uprawnień do odczytu do rozliczeń zasobnika.

1. Zaloguj się do konsoli usług AWS pod adresem https://console.aws.amazon.com i wybierz **usług**.
2. Usługa wyszukiwania wpisz *IAM*i wybierz tę opcję.
3. Wybierz **ról** z menu po lewej stronie.
4. Na liście ról wybierz rolę, utworzonej dla Cloudyn dostępu.
5. Na stronie Podsumowanie ról kliknij, aby skopiować **ARN roli**. Zachowaj ARN roli jest przydatne do wykonania kolejnych kroków.

### <a name="create-an-s3-bucket"></a>Utwórz przedział S3

Możesz utworzyć przedział S3 do przechowywania szczegółowych informacji dotyczących rozliczeń.

1. Zaloguj się do konsoli usług AWS pod adresem https://console.aws.amazon.com i wybierz **usług**.
2. Usługa wyszukiwania wpisz *S3*i wybierz **S3**.
3. Na stronie Amazon S3 kliknij **zasobnika Utwórz**.
4. W kreatorze tworzenia zasobników wybierz nazwa pakietu i Region, a następnie kliknij przycisk **dalej**.  
    ![Utwórz zasobnika](./media/connect-aws-account/create-bucket.png)
5. Na **właściwości** Zachowaj wartości domyślne, a następnie kliknij pozycję **dalej**.
6. Na stronie przeglądu, kliknij przycisk **zasobnika Utwórz**. Zostanie wyświetlony na liście zasobnika.
7. Kliknij zasobnik, który został utworzony i wybierz **uprawnienia** karcie, a następnie wybierz **zasad zasobnik**. Otwiera edytora zasad zasobnika.
8. Skopiuj poniższy przykład JSON i wklej go w edytorze zasad zasobnika.
  - Zastąp `<BillingBucketName>` o nazwie użytkownika przedział S3.
  - Zastąp `<ReadOnlyUserOrRole>` z rolą lub ARN użytkownika, który wcześniej skopiowany.

  ```
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
    ![Zasobnik edytora zasad](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Włączanie usług AWS rozliczeń raportów

Po utworzeniu i skonfigurowaniu przedział S3, przejdź do [rozliczeń preferencje](https://console.aws.amazon.com/billing/home?#/preference) w konsoli usług AWS.

1. Na stronie preferencji wybierz **otrzymywać raporty rozliczeń**.
2. W obszarze **otrzymywać raporty rozliczeń**, wprowadź nazwę zasobnik, który został utworzony, a następnie kliknij przycisk **Sprawdź**.  
3. Wybierz wszystkie cztery szczegółowości opcje raportów, a następnie kliknij przycisk **zapisać preferencje**.  
    ![Włącz raportów](./media/connect-aws-account/enable-reports.png)

Kosztów zarządzania pobiera szczegółowych informacji dotyczących rozliczeń z Twojej przedział S3 i wypełnia raporty po włączeniu szczegółowe rozliczeń. Może potrwać do 24 godzin, aż do szczegółowych danych dotyczących rozliczeń jest wyświetlana w konsoli Cloudyn. Po udostępnieniu szczegółowych danych dotyczących rozliczeń stanie konsolidacji konta jest wyświetlana jako **skonsolidowanych**. Stan konta jest wyświetlany jako **Ukończono**.

![Konto skonsolidowane stanu](./media/connect-aws-account/consolidated-status.png)

Niektóre raporty optymalizacji mogą wymagać dane próbkowania odpowiednich danych należy uzyskać dokładne zalecenia kilka dni.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat usługi Azure koszt zarządzania, w dalszym ciągu [Przejrzyj użycia i koszty](tutorial-review-usage.md) samouczka kosztów zarządzania.
