---
title: Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usług AD DS platformy Azure
description: Dowiedz się, jak instalowanie i konfigurowanie klastra HDInsight Enterprise Security pakietu przy użyciu usługi Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6cfe587abadf8350fecc497b1af1cea9700f4f28
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018730"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usługi Azure Active Directory Domain Services

Klastry Enterprise Security pakietu (ESP) zapewniają dostęp wielu użytkowników w klastrach usługi Azure HDInsight. Klastrami HDInsight przy użyciu ESP są podłączone do domeny, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny przy użyciu klastrów i uruchamiać zadania obsługi danych big data. 

W tym artykule dowiesz się, jak skonfigurować klaster HDInsight przy użyciu ESP przy użyciu Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP jest ogólnie w HDI 3.6 dla platformy Spark, interaktywny i Hadoop. ESP dla typów klastrów HBase i Kafka jest w wersji zapoznawczej.

## <a name="enable-azure-ad-ds"></a>Włączanie usługi Azure AD DS

Włączanie usług AD DS Azure to warunek wstępny, przed utworzeniem klastra HDInsight z ESP. Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Po włączeniu usług AD DS Azure wszystkich użytkowników i obiekty rozpocząć synchronizowanie z usługi Azure Active Directory (AAD) do usługi Azure AD — DS domyślnie. Długość operacji synchronizacji jest zależna od liczby obiektów w usłudze AAD. Synchronizacja może potrwać kilka dni w setkach tysięcy obiektów. 

Klienci mogą wybrać opcję Synchronizuj tylko grupy, którzy potrzebują dostępu do klastrów HDInsight. Tej opcji tylko określone grupy synchronizacji jest nazywany *zakresu synchronizacji*. Zobacz [konfigurowania zakresu synchronizacji z usługi Azure AD do domeny zarządzanej](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) instrukcje.

> [!NOTE]
> Tylko Administratorzy dzierżawy mają uprawnienia do tworzenia wystąpienia usług AD DS platformy Azure. Uwierzytelnianie wieloskładnikowe wymaga można wyłączyć tylko dla użytkowników, którzy będą uzyskiwać dostęp do klastra.

Podczas włączania bezpiecznego protokołu LDAP, umieść nazwę domeny do nazwy podmiotu lub alternatywną nazwę podmiotu w certyfikacie. Na przykład, jeśli nazwa domeny to *contoso.com*, upewnij się, takiej samej nazwie istnieje w nazwie podmiotu certyfikatu lub alternatywnej nazwy podmiotu. Aby uzyskać więcej informacji, zobacz [domeny zarządzanej przez Konfigurowanie bezpiecznego protokołu LDAP dla usługi Azure AD — DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Sprawdź stan kondycji DS usługi AAD

Wyświetlanie stanu kondycji usługi Azure Active Directory Domain Services, wybierając **kondycji** w obszarze **Zarządzaj** kategorii. Upewnij się, że jest w stanie DS usługi AAD, kolor zielony (uruchomione), a synchronizacja została zakończona.

![Kondycja usługi Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Dodaj tożsamość zarządzaną

Tworzenie tożsamości zarządzanych przypisanych przez użytkownika, jeśli nie masz jeszcze takiego. Zobacz [Utwórz, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) instrukcje. 

Tożsamość zarządzana jest używany do upraszczają operacje usługi domeny. Ta tożsamość ma dostęp do odczytu, tworzenie, modyfikowanie i usuwanie operacji usług domeny, które są wymagane przez pakiet zabezpieczeń przedsiębiorstwa HDInsight, takie jak tworzenie jednostek organizacyjnych i zasad usługi.

Po włączeniu usług AD DS platformy Azure, tworzenie zarządzanych tożsamości przypisanych przez użytkownika i przypisz ją do **Współautor usługi domeny HDInsight** roli kontroli dostępu usług AD DS w platformie Azure.

![Usługa Azure Active Directory Domain Services dostępu formantu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisywanie zarządzanych tożsamości w celu **Współautor usługi domeny HDInsight** roli gwarantuje, że tożsamość ma prawidłowy dostęp do wykonywania pewnych operacji usług domeny w domenie usługi Katalogowej usługi AAD. Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Tworzenie klastra HDInsight z ESP

Następnym krokiem jest do utworzenia klastra HDInsight z ESP włączone za pomocą usług AD DS platformy Azure.

Łatwiej można umieścić wystąpienia usług AD DS Azure wraz z klastrów HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli zdecydujesz się umieścić je w różnych sieciach wirtualnych, tak aby maszyny wirtualne HDInsight mają linii wzroku do kontrolera domeny do przyłączania maszyn wirtualnych musi komunikacji równorzędnej tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md). Aby sprawdzić, czy komunikacja równorzędna jest prawidłowa, dołączyć Maszynę wirtualną do HDInsight sieci Wirtualnej/podsieci i wysłać polecenie ping nazwa domeny lub uruchom **ldp.exe** do domeny, dostęp do usługi Katalogowej usługi AAD.

Podczas tworzenia klastra usługi HDInsight, masz możliwość włączenia pakiet Enterprise Security w karcie niestandardowej. 

![Zabezpieczenia usługi Azure HDInsight i sieci](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Po włączeniu ESP, typowych błędów konfiguracji związane z usługi Azure AD — DS będzie można automatycznie wykryte i sprawdzone.

![Weryfikacja domeny pakietu zabezpieczeń Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Wczesne wykrywanie pozwala zaoszczędzić czas, co pozwala naprawić błędy przed utworzeniem klastra.

![Pakiet zabezpieczeń w usłudze Azure HDInsight przedsiębiorstwa nie powiodła się weryfikacja domeny](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Po utworzeniu klastra HDInsight z ESP, musisz podać następujące parametry:

- **Administrator klastra**: Wybierz z usługi zsynchronizowanych Azure AD — DS Administrator dla klastra. To konto musi być już zsynchronizowane i dostępne w usługach Domenowych AAD w.

- **Klaster grup dostępu**: grupy zabezpieczeń, w której użytkownicy mają być synchronizowane w klastrze powinna być dostępna w usłudze Azure AD-DS. Na przykład HiveUsers grupy. Aby uzyskać więcej informacji, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **Adres URL protokołu LDAPS**: są to na przykład ldaps://contoso.com:636.

Poniższy zrzut ekranu przedstawia pomyślnie konfiguracji w witrynie Azure portal:

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Tożsamość zarządzaną, który został utworzony można wybrać w z listy rozwijanej przypisanych przez użytkownika z tożsamości zarządzanej podczas tworzenia nowego klastra.

![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie zasad usługi Hive i uruchamiania zapytań programu Hive, zobacz [Konfigurowanie zasad usługi Hive dla HDInsight klastrów przy użyciu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami HDInsight przy użyciu ESP, przy użyciu protokołu SSH, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
