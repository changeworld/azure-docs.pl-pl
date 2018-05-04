---
title: Konfigurowanie kont magazynu dla usługi Azure Management koszt | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania konta magazynu platformy Azure i usług AWS zasobników magazynu Azure kosztów zarządzania.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 76a9fc586a1932b8b5e664b6c964f0c7d3eac4d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-storage-accounts-for-cost-management"></a>Konfigurowanie kont magazynu koszt zarządzania

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

Raporty zarządzania koszt można zapisywać w portalu Cloudyn, magazynu Azure lub usług AWS magazynu zasobników. Zapisywanie raportów do portalu Cloudyn jest bezpłatne. Jednak zapisywanie raportów w magazynie dostawcy usług chmury jest opcjonalna i wiąże się z dodatkowych kosztów. W tym artykule opisano, jak skonfigurować konta magazynu platformy Azure i Amazon Web Services (AWS) zasobników magazynu do przechowywania raportów.

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć konto magazynu platformy Azure lub Amazon zasobnika magazynu.

Jeśli nie masz konta magazynu platformy Azure, należy go utworzyć. Aby uzyskać więcej informacji o tworzeniu konta magazynu platformy Azure, zobacz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Jeśli nie masz AWS zasobnika usługi (S3) proste magazynu, należy utworzyć jeden. Aby uzyskać więcej informacji o tworzeniu przedział S3, zobacz [utworzyć zasobnik](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurowanie konta usługi Azure storage

Konfigurowanie możesz magazynu Azure do użycia przez koszt zarządzania jest prosta. Zbieranie informacji o koncie magazynu, a następnie skopiuj je w portalu Cloudyn.

1. Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.
2. Kliknij przycisk **wszystkie usługi**, wybierz pozycję **kont magazynu**, przewiń do konta magazynu, który chcesz użyć, a następnie wybierz konta.
3. Na stronie konta magazynu w obszarze **ustawienia**, kliknij przycisk **klucze dostępu**.
4. Kopia programu **nazwy konta magazynu** i **ciąg połączenia** w obszarze klucz1.  
![Klucze dostępu do magazynu Azure](./media/storage-accounts/azure-storage-access-keys.png)  
5. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
6. Kliknij koło zębate symbol, a następnie wybierz **Zarządzanie raportami magazynowania**.
7. Kliknij przycisk **Dodaj nowy +** i upewnij się, że wybrany jest Microsoft Azure. Wklej nazwę konta magazynu Azure w **nazwa** obszaru. Wklej Twojej **ciąg połączenia** w odpowiadający mu obszar. Wprowadź nazwę kontenera, a następnie kliknij przycisk **zapisać**.  
![Magazyn Cloudyn skonfigurowane dla platformy Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Nowy wpis magazynu Azure raportu zostanie wyświetlony na liście kont magazynu.  
    ![Nowy magazyn Azure raportu na liście](./media/storage-accounts/azure-storage-entry.png)


Raporty można teraz zapisać do magazynu Azure. W dowolnym raportu, kliknij polecenie **akcje** , a następnie wybierz **zaplanować raport**. Nazwa raportu i następnie dodać własne adresu URL albo użyj automatycznie utworzone adresu URL. Wybierz **Zapisz do magazynu** , a następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurowanie usług AWS zasobnika magazynu

Cloudyn używa istniejących poświadczeń usług AWS: użytkownik lub rola, aby zapisać raporty z zasobnika. Aby przetestować dostępu, Cloudyn próbuje zapisać to mały plik tekstowy zasobnika z nazwą pliku _wyboru zasobnika permission.txt_.

Osobie Cloudyn roli lub użytkownika z uprawnieniami PutObject Twojego zasobnika. Następnie użyj zasobnika istniejącą lub Utwórz nowe hasło, aby zapisać raportów. Na koniec decyzję dotyczącą sposobu zarządzania Klasa magazynu, ustawić reguły cyklu życia lub usuń niepotrzebne pliki.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Przypisywanie uprawnień do usług AWS użytkownika lub roli

Podczas tworzenia nowych zasad, musisz podać dokładny uprawnienia potrzebne do zapisania raportu do przedział S3.

1. Zaloguj się do konsoli usług AWS i wybierz **usług**.
2. Wybierz **IAM** na liście usług.
3. Wybierz **zasady** po lewej stronie konsoli, a następnie kliknij przycisk **Utwórz zasady**.
4. Kliknij przycisk **JSON** kartę.
5. Następujące zasady umożliwia zapisywanie raportu do przedział S3. Skopiuj i wklej poniższy przykład zasad, aby **JSON** kartę. Zastąp &lt;bucketname&gt; nazwą zasobnika.

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. Kliknij przycisk **Przejrzyj zasady**.  
    ![Przejrzyj zasady](./media/storage-accounts/aws-policy.png)  
7. Na stronie Przegląd zasad wpisz nazwę zasady. Na przykład _CloudynSaveReport2S3_.
8. Kliknij przycisk **Tworzenie zasad**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Dołącz zasady do roli Cloudyn lub użytkownika w ramach Twojego konta

Aby dołączyć nowe zasady, otwórz konsolę usług AWS i edytować rolę Cloudyn lub użytkownika.

1. Zaloguj się do konsoli usług AWS i wybierz **usług**, a następnie wybierz pozycję **IAM** na liście usług.
2. Wybierz opcję **ról** lub **użytkowników** po lewej stronie konsoli.

**Dla ról:**

  1. Kliknij nazwę roli Cloudyn.
  2. Na **uprawnienia** , kliknij pozycję **Dołącz zasady**.
  3. Wyszukaj utworzone zasady i zaznacz go, a następnie kliknij przycisk **Dołącz zasady**.
    ![Usług AWS - dołączyć zasad dla roli](./media/storage-accounts/aws-attach-policy-role.png)

**Dla użytkowników:**

1. Wybierz użytkownika Cloudyn.
2. Na **uprawnienia** , kliknij pozycję **dodać uprawnienia**.
3. W **Udziel uprawnień** zaznacz **dołączyć istniejących zasad bezpośrednio**.
4. Wyszukaj utworzone zasady i zaznacz go, a następnie kliknij przycisk **dalej: Przejrzyj**.
5. Dodaj uprawnienia do strony Nazwa roli, wybierz polecenie **dodać uprawnienia**.  
    ![Usług AWS - Dołącz zasady dla użytkownika](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcjonalnie: Ustaw uprawnienia z zasadami zasobnika

Można również ustawić uprawnienia do tworzenia raportów w Twojej przedział S3 przy użyciu zasad zasobnika. W widoku klasycznym S3:

1. Utwórz lub wybierz istniejący zasobnika.
2. Wybierz **uprawnienia** a następnie kliknij pozycję **pojemnik zasad**.
3. Skopiuj i wklej poniższy przykład zasad. Zastąp &lt;zasobnik\_nazwa&gt; i &lt;Cloudyn\_zasady&gt; z ARN z Twojej zasobnika. Zastąp ARN roli lub użytkownik w Cloudyn.

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. W edytorze zasad zasobnika kliknij **zapisać**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Dodawanie magazynu raportu usług AWS do Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
2. Kliknij koło zębate symbol, a następnie wybierz **Zarządzanie raportami magazynowania**.
3. Kliknij przycisk **Dodaj nowy +** i upewnij się, że wybrano AWS.
4. Wybierz zasobnik konta i magazynu. Nazwa zasobnika magazynu usług AWS jest wypełniane automatycznie.  
    ![Dodawanie magazynu raportu dotyczącego zasobnika usług AWS](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kliknij przycisk **zapisać** , a następnie kliknij przycisk **Ok**.

    Nowy wpis magazynu raportu usług AWS pojawia się na liście kont magazynu.  
    ![Nowy magazyn raportu usług AWS na liście](./media/storage-accounts/aws-storage-entry.png)


Raporty można teraz zapisać do magazynu Azure. W dowolnym raportu, kliknij polecenie **akcje** , a następnie wybierz **zaplanować raport**. Nazwa raportu i następnie dodać własne adresu URL albo użyj automatycznie utworzone adresu URL. Wybierz **Zapisz do magazynu** , a następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [opis koszt raporty zarządzania](understanding-cost-reports.md) informacje na temat podstawowej struktury i funkcje, koszt zarządzania raportów.
