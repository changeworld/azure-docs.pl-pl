---
title: Konfigurowanie kont magazynu na platformie Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania konta usługi Azure storage i zasobników magazynu AWS na platformie Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: f7092a08e501ae61ef93be383290db575b5ad1f1
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995579"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurowanie kont magazynu na platformie Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Raporty usługi Cloudyn można zapisać w portalu Cloudyn, usługi Azure storage lub usług AWS magazyn zasobników. Zapisywanie raportów w portalu Cloudyn jest bezpłatne. Jednak zapisywanie raportów magazynu dostawcy usług chmury jest opcjonalna i spowoduje naliczenie dodatkowych kosztów. Ten artykuł pomoże Ci skonfigurować konto magazynu Azure i Amazon Web Services (AWS) zasobników magazynu do przechowywania raportów.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto usługi Azure storage lub zasobnika magazynu Amazon.

Jeśli nie masz konta usługi Azure storage, należy ją utworzyć. Aby uzyskać więcej informacji na temat tworzenia konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

Jeśli nie masz usługi AWS simple storage service (S3) zasobnik, należy ją utworzyć. Aby uzyskać więcej informacji na temat tworzenia przedział S3, zobacz [Tworzenie koszyka](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurowanie konta usługi Azure storage

Możesz konfigurowania usługi Azure storage na potrzeby używania przez firmę Cloudyn jest bardzo proste. Zbieranie informacji o koncie magazynu, a następnie skopiuj je w portalu Cloudyn.

1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
2. Kliknij przycisk **wszystkich usług**, wybierz opcję **kont magazynu**, przewiń do konta magazynu, który chcesz użyć, a następnie wybierz konto.
3. Na stronie Twojego konta magazynu w ramach **ustawienia**, kliknij przycisk **klucze dostępu**.
4. Kopiuj usługi **nazwa konta magazynu** i **parametry połączenia** w obszarze klucz1.  
![Klucze dostępu usługi Azure storage](./media/storage-accounts/azure-storage-access-keys.png)  
5. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
6. Kliknij symbol koła zębatego, a następnie wybierz pozycję **raporty zarządzania magazynem**.
7. Kliknij przycisk **Dodaj nowe +** i upewnij się, że wybrano Microsoft Azure. Wklej nazwę konta usługi Azure storage w **nazwa** obszaru. Wklej swoje **parametry połączenia** w odpowiadający mu obszar. Wprowadź nazwę kontenera, a następnie kliknij przycisk **Zapisz**.  
![Magazyn Cloudyn skonfigurowane dla platformy Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Nowy wpis raport usługi Azure storage, zostanie wyświetlony na liście kont magazynu.  
    ![Nowy magazyn Azure raportu na liście](./media/storage-accounts/azure-storage-entry.png)


Raporty można zapisać teraz w usłudze Azure storage. W dowolnym raporcie kliknij **akcje** , a następnie wybierz **zaplanować raport**. Nazywanie raportu i następnie dodać własnego adresu URL lub użyć automatycznie utworzonego adresu URL. Wybierz **zapisywanie w magazynie** i następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurowanie przedziału magazyn AWS

Cloudyn przy użyciu istniejących poświadczeń usługi AWS: użytkownika lub roli, aby zapisać raporty do zasobnika usługi. Aby przetestować dostęp, Cloudyn próbuje zapisać to mały plik tekstowy do zasobnika z nazwą pliku _wyboru zasobnika permission.txt_.

Podasz roli Cloudyn lub użytkownika z uprawnieniami PutObject do zasobnika usługi. Następnie użyj istniejącego zasobnika, lub Utwórz nowy, aby zapisywać raporty. Na koniec zdecyduj, jak zarządzać klasę magazynu, Ustaw zasady cyklu życia lub usuń niepotrzebne pliki.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Przypisz uprawnienia użytkownika usług AWS lub roli

Podczas tworzenia nowych zasad, musisz podać uprawnienia potrzebne, aby zapisać raport przedział S3.

1. Zaloguj się do konsoli usług AWS, a następnie wybierz pozycję **usług**.
2. Wybierz **IAM** z listy usług.
3. Wybierz **zasady** po lewej stronie konsoli, a następnie kliknij przycisk **Utwórz zasady**.
4. Kliknij przycisk **JSON** kartę.
5. Następujące zasady umożliwia zapisywanie raportu przedział S3. Skopiuj i wklej poniższy przykład zasad **JSON** kartę. Zastąp &lt;bucketname&gt; z Twoją nazwą przedziału.

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
    ![Przegląd zasad](./media/storage-accounts/aws-policy.png)  
7. Na stronie Przegląd zasad wpisz nazwę dla zasad. Na przykład _CloudynSaveReport2S3_.
8. Kliknij przycisk **Tworzenie zasad**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Dołącz zasady do rozwiązania Cloudyn roli lub użytkownika w ramach Twojego konta

Aby dołączyć nowe zasady, otwórz konsolę usług AWS i edytować rolę Cloudyn lub użytkownika.

1. Zaloguj się do konsoli usług AWS, a następnie wybierz pozycję **usług**, a następnie wybierz **IAM** z listy usług.
2. Wybierz opcję **role** lub **użytkowników** po lewej stronie konsoli.

**Dla ról:**

  1. Kliknij nazwę roli Cloudyn.
  2. Na **uprawnienia** kliknij pozycję **Dołącz zasady**.
  3. Wyszukiwanie zasad, który został utworzony i wybierz ją, a następnie kliknij przycisk **Dołącz zasady**.
    ![Usługi AWS — Dołączanie zasad dla roli](./media/storage-accounts/aws-attach-policy-role.png)

**Dla użytkowników:**

1. Wybierz użytkownika platformy Cloudyn.
2. Na **uprawnienia** kliknij pozycję **Dodaj uprawnienia**.
3. W **Udziel uprawnienia** zaznacz **Dołącz istniejące zasady bezpośrednio**.
4. Wyszukiwanie zasad, który został utworzony i wybierz ją, a następnie kliknij przycisk **dalej: Przejrzyj**.
5. Dodaj uprawnienia do roli nazwy strony, kliknij przycisk **Dodaj uprawnienia**.  
    ![Usługi AWS — Dołączanie zasad użytkownika](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcjonalnie: Ustawianie uprawnień za pomocą zasad zasobnika

Można również ustawić uprawnienia do tworzenia raportów w Twojej przedział S3 przy użyciu zasad zasobnika. W widoku klasycznym S3:

1. Utwórz lub wybierz istniejące przedziału.
2. Wybierz **uprawnienia** kartę, a następnie kliknij przycisk **zasobnika zasad**.
3. Skopiuj i wklej poniższy przykład zasad. Zastąp &lt;zasobnika\_nazwa&gt; i &lt;Cloudyn\_zasady&gt; z ARN z przedziału sieci. Zastąp ARN roli lub użytkownik w rozwiązaniu Cloudyn.

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

4. W edytorze zasad zasobnika kliknij **Zapisz**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Dodaj magazyn raportu usług AWS do rozwiązania Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **raporty zarządzania magazynem**.
3. Kliknij przycisk **Dodaj nowe +** i upewnij się, że wybrano AWS.
4. Wybierz przedział konta i magazynu. Nazwa zasobnika magazynu AWS jest wypełniane automatycznie.  
    ![Dodaj magazyn raportu dla przedziału usług AWS](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kliknij przycisk **Zapisz** a następnie kliknij przycisk **Ok**.

    Nowy wpis magazynu raportu AWS pojawia się na liście kont magazynu.  
    ![Nowy magazyn raportu usług AWS na liście](./media/storage-accounts/aws-storage-entry.png)


Raporty można zapisać teraz w usłudze Azure storage. W dowolnym raporcie kliknij **akcje** , a następnie wybierz **zaplanować raport**. Nazywanie raportu i następnie dodać własnego adresu URL lub użyć automatycznie utworzonego adresu URL. Wybierz **zapisywanie w magazynie** i następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [Cloudyn zrozumienia raporty](understanding-cost-reports.md) informacje na temat podstawowej struktury oraz funkcji Raporty usługi Cloudyn.
