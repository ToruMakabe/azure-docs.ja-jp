---
title: "Azure Files についてよく寄せられる質問 | Microsoft Docs"
description: "Azure Files についてよく寄せられる質問とその回答を紹介します。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: da8ccf35dcc873a5c31842c6eb7bdf72879854c2
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Azure Files に関してよく寄せられる質問
[Azure Files](storage-files-introduction.md) は、クラウド上で、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (別称 Common Internet File System または CIFS) を介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure ファイル共有は、クラウドまたはオンプレミスにデプロイされた Windows、Linux、macOS で同時にマウントできます。 また、データが使用される場所に近接した Windows Server マシンに、Azure File Sync (プレビュー) で Azure ファイル共有をキャッシュすることによって高速なアクセスを実現することもできます。

この記事では、Azure Files での Azure File Sync の使用を含め、Azure Files の機能についてよく寄せられる質問に答えます。 質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Storage フォーラム](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazuredata)。
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。 
4. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、**[ヘルプとサポート]** ボタンを選択し、**[新しいサポート要求]** を選択します。

## <a name="general"></a>全般
* <a id="why-files-useful"></a>**Azure Files はどのように便利なのですか。**  
   Azure Files を使用すると、物理サーバー、デバイス、アプライアンスに伴うオーバーヘッドの管理に縛られることなく、クラウドにファイル共有を作成することができます。 OS 更新プログラムの適用、不良ディスクの交換など、単調な作業は Microsoft が代わって行います。 Azure Files を利用できるシナリオの詳細については、「[Azure Files の適用ケース](storage-files-introduction.md#why-azure-files-is-useful)」をご覧ください。

* <a id="file-access-options"></a>**Azure Files でファイルにアクセスするさまざまな方法を挙げてください。**  
    SMB 3.0 プロトコルを使用してローカル マシンにファイル共有をマウントするか、[Storage Explorer](http://storageexplorer.com/) などのツールを使用してファイル共有内のファイルにアクセスできます。 アプリケーションから、ストレージ クライアント ライブラリ、REST API、PowerShell、または Azure CLI を使用して、Azure ファイル共有内のファイルにアクセスできます。

* <a id="what-is-afs"></a>**Azure ファイル同期とは何ですか。**  
    Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server マシンが Azure ファイル共有の高速キャッシュに変わります。 SMB、ネットワーク ファイル システム (NFS)、ファイル転送プロトコル サービス (FTPS) など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルでアクセスすることができます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

* <a id="files-versus-blobs"></a>**データに Azure ファイル共有または Azure Blob Storage を使用する理由を教えてください。**  
    Azure Files と Azure Blob Storage はどちらも、クラウドに大量のデータを保存する手段として利用できますが、その用途にやや違いがあります。 
    
    Azure Blob ストレージは、構造化されていないデータを格納する必要がある大規模なクラウド ネイティブ アプリケーションに役立ちます。 パフォーマンスとスケールを最大限に高めるには、Azure Blob ストレージは、実際のファイル システムよりも単純なストレージの抽出になります。 Azure Blob Storage には必ず、REST ベースのクライアント ライブラリ経由で (または REST ベースのプロトコルを介して直接) アクセスする必要があります。

    Azure Files は、厳密にはファイル システムです。 Azure Files には、オンプレミスのオペレーティング システムで長年使われてきた、皆さんもおなじみのファイルの概念がすべて備わっています。 Azure Blob Storage と同様、Azure Files にも REST インターフェイスと REST ベースのクライアント ライブラリが用意されています。 Azure Files が Azure Blob Storage と違うのは、Azure ファイル共有に SMB でアクセスできることです。 SMB を使用すれば、オンプレミスの VM であれ、クラウドの VM であれ、Windows、Linux、macOS 上で Azure ファイル共有を直接マウントすることができます。コードを記述したり、特別なドライバーをファイル システムにアタッチする必要はありません。 また、データが使用される場所に近接したオンプレミスのファイル サーバーに、Azure File Sync で Azure ファイル共有をキャッシュすることによって高速なアクセスを実現することもできます。 
   
    Azure Files と Azure Blob Storage の違いに関する詳細な説明については、「[Azure BLOB、Azure Files、Azure ディスクの使い分け](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」をご覧ください。 Azure Blob ストレージの詳細については、「[Blob ストレージの概要](../blobs/storage-blobs-introduction.md)」をご覧ください。

* <a id="files-versus-disks"></a>**Azure ディスクではなく Azure ファイル共有を使用する理由を教えてください。**  
    Azure ディスクにおけるディスクは、ただのディスクに過ぎません。 ディスク単体では、利便性に限りがあります。 Azure ディスクを有効活用するには、Azure で実行されている仮想マシンにディスクをアタッチする必要があります。 Azure ディスクは、オンプレミスのサーバーで使われるディスクとまったく同じ用途で使用することができます。 これは、OS システム ディスクや OS のスワップ領域、アプリケーションの専用ストレージとして使用可能です。 Azure ディスクの興味深い使用方法としては、Azure ファイル共有を使う場所と同じ場所で使用するために、クラウドにファイル サーバーを作成することがあります。 ファイル サーバーを Azure Virtual Machines にデプロイすることは、Azure Files では現在サポートされていないデプロイ オプションを必要とする場合 (NFS プロトコル サポートや Premium Storage など) に Azure でファイル ストレージを取得するための、高パフォーマンスの優れた方法です。 

    一方で、Azure ディスクを保持するファイル サーバーをバックエンド ストレージとして実行すると、いくつかの理由から、通常は Azure ファイル共有を使用するよりもはるかに高コストになります。 第一に、ディスク ストレージの料金を支払うだけでなく、1 つまたは複数の Azure VM を実行する費用を支払う必要があります。 第二に、ファイル サーバーの実行に使用される VM を管理する必要もあります。 たとえば OS のアップグレードを自分で行う必要があります。 最後に、オンプレミスでデータをキャッシュする必要が最終的に生じた場合、分散ファイル システム レプリケーション (DFSR) などのレプリケーション テクノロジをユーザーの判断で設定して管理し、キャッシュを行う必要があります。

    (Azure ディスクをバックエンド ストレージとして使用することに加え) Azure Virtual Machines にホストされたファイル サーバーと Azure Files の両方の利点を活かす方法の 1 つは、クラウド VM 上でホストされているファイル サーバーに Azure File Sync をインストールすることです。 Azure ファイル共有がお使いのファイル サーバーと同じリージョンにある場合、クラウドの階層化を有効にして、ボリュームの空き領域を最大 (99%) に設定できます。 これによりデータの重複を最小限に抑えることができます。 また、NFS プロトコル サポートを必要とするアプリケーションなど、どのようなアプリケーションでも必要に応じてファイル サーバーで使用することができます。

    Azure で高パフォーマンスで高可用なファイル サーバーを設定する方法については、「[Deploying IaaS VM Guest Clusters in Microsoft Azure (Microsoft Azure に IaaS VM ゲスト クラスターをデプロイする)](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)」をご覧ください。 Azure Files と Azure ディスクの違いに関する詳細な説明については、「[Azure BLOB、Azure Files、Azure ディスクの使い分け](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」をご覧ください。 Azure ディスクの詳細については、「[Azure Managed Disks の概要](../../virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

* <a id="get-started"></a>**どのようにすれば Azure Files の使用を開始できますか。**  
   Azure Files の使い方は簡単です。 まず[ファイル共有を作成](storage-how-to-create-file-share.md)し、お好きなオペレーティング システムでマウントします。 

    * [Windows でのマウント](storage-how-to-use-files-windows.md)
    * [Linux でのマウント](storage-how-to-use-files-linux.md)
    * [macOS でのマウント](storage-how-to-use-files-mac.md)

   Azure ファイル共有をデプロイして組織内の運用ファイル共有を置き換える方法についての詳しいガイドは、「[Azure Files のデプロイの計画](storage-files-planning.md)」をご覧ください。

* <a id="redundancy-options"></a>**Azure Files では、どのようなストレージ冗長性オプションがサポートされていますか。**  
    Azure Files では現在、ローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) のみがサポートされます。 今後、ゾーン冗長ストレージ (ZRS) と読み取りアクセスの geo 冗長ストレージ (RA-GRS) をサポートすることを計画していますが、現時点でスケジュールをお伝えすることはできません。

* <a id="tier-options"></a>**Azure Files では、どのストレージ層がサポートされていますか。**  
    現在 Azure Files でサポートされているのは、標準ストレージ層のみです。 Premium Storage およびクール ストレージのサポートについては、現時点でスケジュールをお伝えすることはできません。 
    
    > [!NOTE]
    > BLOB のみのストレージ アカウントと Premium Storage アカウントからは、Azure ファイル共有を作成できません。

* <a id="give-us-feedback"></a>**ある特定の機能が Azure Files に追加されることを個人的に望んでいます。それを追加していただくことはできますか。**  
    Azure Files チームは、サービスに関するユーザーからのあらゆるフィードバックに耳を傾けたいと思っています。 [Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) まで機能に関する要望をお寄せください。 多くの新しい機能を皆様に喜んでいただけることを楽しみにしています。

## <a name="azure-file-sync"></a>Azure ファイル同期
* <a id="afs-region-availability"></a>**Azure File Sync (プレビュー) は、どのリージョンでサポートされていますか。**  
    Azure File Sync は現在、米国西部、西ヨーロッパ、オーストラリア東部、および東南アジアで利用可能です。 一般公開に向けての作業が進むにつれ、サポートされるリージョンがさらに追加される予定です。 詳細については、「[利用可能なリージョン](storage-sync-files-planning.md#region-availability)」をご覧ください。

* <a id="cross-domain-sync"></a>**同じ同期グループ内にドメイン参加とドメイン非参加のサーバーを保持することはできますか。**  
    はい。 異なる Active Directory に属しているサーバー エンドポイントを 1 つの同期グループに含めることは可能です。ドメインに参加していない場合でも同様です。 この構成は技術的には機能するものの、通常の構成としては、お勧めできません。サーバー上のファイル/フォルダー用に定義されたアクセス制御リスト (ACL) は、同期グループ内の他のサーバーでは適用できない可能性があるためです。 最良の結果を得るには、同じ Active Directory フォレスト内にあるサーバー間、別の Active Directory フォレスト内にあるものの信頼関係が確立されているサーバー間、または 1 つのドメインにはないサーバー間で同期することをお勧めします。 これらの構成を混在させることは避けるようにしてください。

* <a id="afs-change-detection"></a>**SMB またはポータルで Azure ファイル共有に直接ファイルを作成しました。このファイルが同期グループのサーバーに同期されるまでどのくらいの時間がかかりますか。**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**2 つのサーバーで同じファイルがほぼ同時に変更された場合、どうなりますか。**  
    Azure File Sync では、シンプルな競合解決方法が採用されています。ファイルに対して 2 つのサーバーで同時に変更を加えた場合、その両方の変更が保持されます。 最後に書き込まれた変更では、元のファイル名が維持されます。 古いファイルでは、"ソース" マシンと競合番号が名前に追加されます。 次の命名規則が使用されます。 
   
    \<拡張子を除くファイル名\>-\<マシン名\>\[-#\].\<拡張子\>  

    たとえば、CompanyReport.docx で競合が生じたとします。古い方の書き込みが CentralServer で行われた場合、最初の競合で生じるファイルの名前は CompanyReport-CentralServer.docx となります。 2 回目の競合では、CompanyReport-CentralServer-1.docx という名前になります。

* <a id="afs-storage-redundancy"></a>**Azure File Sync では、geo 冗長ストレージはサポートされますか。**  
    はい。Azure Files では、ローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) の両方がサポートされます。 ペアになっているリージョン間で GRS フェールオーバーが発生する場合、新しいリージョンはデータのバックアップとしてのみ扱うことをお勧めします。 Azure File Sync は、新しいプライマリ リージョンで自動的に同期を開始することはありません。 

* <a id="sizeondisk-versus-size"></a>**ファイルの *[ディスク上のサイズ]* プロパティが、Azure ファイル同期を使用した後の *[サイズ]* プロパティと一致しないのはどうしてですか。**  
    Windows のエクスプローラーでは、**[サイズ]** と **[ディスク上のサイズ]** という、ファイルのサイズを表現する 2 つのプロパティを公開しています。 これらのプロパティは、意味が微妙に異なります。 **[サイズ]** は、ファイルそのもののサイズを表します。 **[ディスク上のサイズ]** は、ディスクに格納されているファイル ストリームのサイズを表します。 圧縮、データ重複除去の使用、Azure File Sync の使用によるクラウド階層化など、さまざまな理由から、これらのプロパティの値が異なる場合があります。ファイルが Azure ファイル共有に階層化された場合、ディスク上ではなく Azure ファイル共有にファイル ストリームが格納されるため、ディスク上のサイズはゼロになります。 また、ファイルが部分的に階層化される (または、部分的に再現される) 場合もあります。 部分的に階層化されたファイルでは、ファイルの一部がディスク上に存在します。 これは、マルチメディア プレーヤーや圧縮ユーティリティなどのアプリケーションによってファイルが部分的に読み取られた場合に、発生する可能性があります。 

* <a id="is-my-file-tiered"></a>**ファイルが階層化されているかどうかは、どうやって判断できますか。**  
    ファイルが Azure ファイル共有に階層化されたかどうかを確認するには、いくつかの方法があります。
    
   *  **ファイル上でファイル属性を確認する**
     これを行うには、ファイルを右クリックして **[詳細]** に移動し、**[属性]** プロパティまで下へスクロールします。 階層化されたファイルには、次のような属性設定があります。     
        
        | 属性の文字 | Attribute | 定義 |
        |:----------------:|-----------|------------|
        | A | Archive | バックアップ ソフトウェアによって、ファイルがバックアップされる必要があることを示します。 この属性は、ファイルが階層化されているか、ディスク上に完全に格納されているかに関係なく、常に設定されます。 |
        | P | スパース ファイル | ファイルがスパース ファイルであることを示します。 スパース ファイルとは、ディスク ストリーム上のファイルがほぼ空である場合に、効率的に使用するために NTFS が提供している特別なファイルの種類です。 ファイルは完全に階層化されているか、または部分的に再現されているため、Azure File Sync ではスパース ファイルが使用されます。 完全に階層化されたファイルでは、ファイル ストリームがクラウドに格納されています。 部分的に再現されているファイルでは、ファイルの一部が既にディスク上に存在します。 ファイルがディスク上に完全に再現されている場合、Azure File Sync では、スパース ファイルから通常のファイルに変換します。 |
        | L | 再解析ポイント | ファイルが再解析ポイントを保持していることを示します。 再解析ポイントは、ファイル システム フィルターで使用するための特殊なポインターです。 Azure File Sync では、クラウド内でファイルが格納される Azure File Sync のファイル システム フィルター (StorageSync.sys) に対して定義した再解析ポイントを使用します。 これにより、シームレスなアクセスが可能になります。 Azure File Sync が使用されていることや、Azure ファイル共有に格納されているファイルへのアクセス方法をユーザーが知る必要はありません。 ファイルが完全に再現されている場合、Azure ファイル同期では、そのファイルから再解析ポイントを削除します。 |
        | O | オフライン | ファイルのコンテンツの一部またはすべてがディスク上に保存されていないことを示します。 ファイルが完全に再現されている場合、Azure ファイル同期ではこの属性を削除します。 |

        ![[詳細] タブが選択されている、ファイルの [プロパティ] ダイアログ ボックス](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        また、**[属性]** フィールドをエクスプローラーのテーブル表示に追加することで、フォルダー内にあるすべてのファイルの属性を確認できます。 これを行うには、既存の列 (たとえば、**[サイズ]**) を右クリックして、**[詳細]** を選択し、ドロップダウン リストから **[属性]** を選択します。
        
   * **`fsutil` を使用して、ファイル上の再解析ポイントを確認します。**
       前記のオプションで説明したように、階層化されたファイルには必ず再解析ポイントが設定されます。 再解析ポインターは、Azure File Sync のファイル システム フィルター (StorageSync.sys) の特別なポインターです。 ファイルに再解析ポイントがあるかどうかを調べるには、管理者特権でのコマンド プロンプトまたは PowerShell ウィンドウで、`fsutil` ユーティリティを実行します。
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        ファイルに再解析ポイントがある場合、**Reparse Tag Value : 0x8000001e** と表示されます。 この 16 進値は、Azure File Sync が所有する再解析ポイントの値です。また、出力には、Azure ファイル共有上のファイルへのパスを表す再解析データが含まれます。

        > [!WARNING]  
        > `fsutil reparsepoint` ユーティリティ コマンドには、再解析ポイントを削除する機能も含まれています。 Azure File Sync のエンジニア チームによって指示されない限り、このコマンドは実行しないでください。 このコマンドを実行すると、データが失われる可能性があります。 

* <a id="afs-recall-file"></a>**使用したいファイルが階層化されています。ローカルで使用するためにこのファイルをディスクに再現するには、どうすればよいですか。**  
    ディスクにファイルを再現する最も簡単な方法は、ファイルを開くことです。 Azure File Sync のファイル システム フィルター (StorageSync.sys) は、Azure ファイル共有からファイルをシームレスにダウンロードします。ユーザー側で作業を行う必要はありません。 マルチメディア ファイルや .zip ファイルなど、部分的に読み取りできるファイルの種類の場合、ファイルを開いても、ファイル全体がダウンロードされることはありません。

    また、PowerShell を使ってファイルを強制的に再現することも可能です。 複数のファイル (フォルダー内にあるすべてのファイルなど) を一度に再現したい場合に、この方法が役立つことがあります。 Azure File Sync がインストールされているサーバー ノードへの PowerShell セッションを開き、次の PowerShell コマンドを実行します。
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**ファイルまたはディレクトリを強制的に階層化するには、どうすればよいですか。**  
    クラウドの階層化機能が有効な場合は、クラウド エンドポイントに指定されているボリューム空き領域の割合を達成するために、最終アクセス時刻と最終変更時刻に基づいて自動的にファイルが階層化されます。 ただし、手動で強制的にファイルを階層化したいこともあります。 これから長期間、再使用する予定がない大きなファイルを保存して、今はボリューム上に他のファイルとフォルダーのための領域を空けておきたい場合、手動による階層化が役立つ可能性があります。 次の PowerShell コマンドを使って、強制的に階層化できます。

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Azure ファイル同期によって自動的に除外されるのは、どのファイルまたはフォルダーですか。**  
    Azure ファイル同期は既定で、次のファイルを除外します。
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    また、既定で、次のフォルダーも除外します。

    * \System Volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>**Windows Server 2008 R2、Linux、または自分のネットワーク接続ストレージ (NAS) デバイスで Azure File Sync を使用することはできますか。**  
    現在 Azure File Sync でサポートされるのは、Windows Server 2016 および Windows Server 2012 R2 のみです。 現時点でお伝えできる他の計画はありませんが、お客様の要望に応じてサポートするプラットフォームを増やしていきたいと考えております。 サポート対象としてご希望のプラットフォームがあれば、[Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) までお寄せください。

## <a name="security-authentication-and-access-control"></a>セキュリティ、認証、およびアクセス制御
* <a id="ad-support"></a>**Azure Files では、Active Directory ベースの認証とアクセス制御はサポートされていますか。**  
    Azure Files では、2 つの方法でアクセス制御を管理することができます。

    - Shared Access Signature (SAS) を使用すると、指定した期間中有効な特定のアクセス許可があるトークンを生成できます。 たとえば、特定のファイルへの読み取り専用のアクセス許可が付与された、有効期限が 10 分間であるトークンを生成できます。 このトークンを所有するすべてのユーザーは、トークンが有効な間、そのファイルへの 10 分間の読み取り専用アクセスを持ちます。 現在、Shared Access Signature キーは、REST API またはクライアント ライブラリでしかサポートされていません。 ストレージ アカウント キーを使って SMB 経由で Azure ファイル共有をマウントする必要があります。

    - Azure File Sync は、(Active Directory ベースかローカルかに関係なく) すべての随意 ACL (DACL) を保持し、同期先のすべてのサーバー エンドポイントにレプリケートします。 Windows Server は既に Active Directory で認証できるので、Azure File Sync は、Active Directory ベースの認証の完全サポートと ACL のサポートが実現するまでの優れた応急策となります。

    現在 Azure Files は、Active Directory を直接はサポートしていません。

* <a id="encryption-at-rest"></a>**Azure ファイル共有に保存時の暗号化を確保するには、どうすればよいですか。**  
    現在、すべてのリージョンにおいて Azure Storage Service Encryption を既定で有効にするプロセスが進行中です。 これらのリージョンでは、暗号化を有効にするために何かする必要はありません。 その他のリージョンについては、[サーバー側の暗号化](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。

* <a id="access-via-browser"></a>**Web ブラウザーを使用して特定のファイルにアクセスできるようにするにはどうすればよいですか。**  
    Shared Access Signature を使用すると、指定した期間中有効な特定のアクセス許可があるトークンを生成できます。 たとえば、特定の期間に特定のファイルに対する読み取り専用アクセス権だけがあるトークンを生成できます。 この URL を持つユーザーは、トークンが有効な間、任意の Web ブラウザーから直接ファイルにアクセスできます。 Shared Access Signature キーは、Storage Explorer などの UI から簡単に生成できます。

* <a id="file-level-permissions"></a>**共有内のフォルダーに読み取り専用または書き込み専用の権限を指定できますか。**  
    SMB を使用してファイル共有をマウントした場合、フォルダーレベルでアクセス許可を制御することはできません。 ただし、REST API またはクライアント ライブラリを使用して Shared Access Signature を作成すれば、共有内のフォルダーに対して読み取り専用または書き込み専用のアクセス許可を指定することができます。

* <a id="ip-restrictions"></a>**Azure ファイル共有に IP 制限を実装することはできますか。**  
    はい。 Azure ファイル共有へのアクセスはストレージ アカウント レベルで制限できます。 詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークの構成 (プレビュー)](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

* <a id="data-compliance-policies"></a>**Azure Files ではどのようなデータ コンプライアンス ポリシーがサポートされていますか。**  
   Azure Files は、Azure Storage 内の他のストレージ サービスと同じストレージ アーキテクチャ上で実行されます。 他の Azure Storage サービスで使用されているデータ コンプライアンス ポリシーが Azure Files でも適用されます。 Azure Storage のデータ コンプライアンスの詳細については、[Microsoft Azure のデータ保護に関するドキュメント](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)をダウンロードして参照したり、[Microsoft セキュリティ センター](https://www.microsoft.com/TrustCenter/default.aspx)にアクセスしたりすることができます。

## <a name="on-premises-access"></a>オンプレミスのアクセス
* <a id="expressroute-not-required"></a>**Azure Files に接続するためや、Azure ファイル同期をオンプレミスで使用するために、Azure ExpressRoute を使用する必要はありますか。**  
    
いいえ。 ExpressRoute から Azure ファイル共有にアクセスする必要はありません。 Azure ファイル共有を直接オンプレミスにマウントしている場合、必要なのは、インターネット アクセスのためにポート 445 (TCP 送信) が開放されていることだけです (これは SMB が通信に使用するポートです)。 Azure File Sync を使用している場合、必要なのは、HTTPS アクセスのためのポート 443 (TCP 送信) だけです (SMB は必要ありません)。 ただし、ExpressRoute は、それらのアクセス オプションのいずれでも使用 "*できます*"。

* <a id="mount-locally"></a>**ローカル マシンに Azure ファイル共有をマウントするにはどうすればよいですか。**  
    ポート 445 (TCP 送信) が開放されており、クライアントが SMB 3.0 プロトコルをサポートしている (たとえば、Windows 10 や Windows Server 2016 を使用している) 場合は、SMB プロトコル経由でファイル共有をマウントできます。 ポート 445 が組織のポリシーまたはお使いの ISP によってブロックされている場合は、Azure File Sync を使用して Azure ファイル共有にアクセスできます。

## <a name="backup"></a>Backup
* <a id="backup-share"></a>**Azure ファイル共有はどのようにバックアップしますか。**  
    誤って削除しないように、定期的に[共有スナップショット (プレビュー)](storage-how-to-use-files-snapshots.md) を使用して保護できます。 AzCopy、RoboCopy、またはマウントされているファイル共有をバックアップできるサードパーティー製のバックアップ ツールを使用することもできます。 

## <a name="share-snapshots"></a>共有スナップショット
### <a name="share-snapshots-general"></a>共有スナップショット - 一般
* <a id="what-are-snaphots"></a>**ファイル共有スナップショットとは何ですか。**  
    Azure ファイル共有スナップショットでは、読み取り専用バージョンのファイル共有を作成できます。 また、Azure Files を使用すると、以前のバージョンのコンテンツをコピーして同じ共有に戻したり、Azure またはオンプレミス内で場所を変更してさらに変更を加えたりできます。 共有スナップショットの詳細については、[共有スナップショットの概要](storage-snapshots-files.md)に関する記事をご覧ください。

* <a id="where-are-snapshots-stored"></a>**共有スナップショットはどこに格納されていますか。**  
    共有スナップショットは、ファイル共有と同じストレージ アカウントに格納されています。

* <a id="snapshot-perf-impact"></a>**共有スナップショットを使用することに関してパフォーマンス上の影響はありますか。**  
    共有スナップショットには、パフォーマンスのオーバーヘッドはありません。

* <a id="snapshot-consistency"></a>**共有スナップショットにアプリケーション整合性はありますか。**  
    いいえ。共有スナップショットにアプリケーション整合性はありません。 共有スナップショットを取得する前に、ユーザーは、アプリケーションから共有に書き込みをフラッシュする必要があります。

* <a id="snapshot-limits"></a>**使用できる共有スナップショットの数に制限はありますか。**  
    はい。 Azure Files で保持できる共有スナップショットの数は最大で 200 個です。 共有スナップショットは共有クォータに対してカウントされないので、すべての共有スナップショットで使用される合計領域に共有ごとの制限はありません。 ストレージ アカウントの制限は、引き続き適用されます。 共有スナップショットの数が 200 を超えると、新しい共有スナップショットを作成するために、古い共有スナップショットを削除する必要があります。

### <a name="create-share-snapshots"></a>共有スナップショットを作成する
* <a id="file-snaphsots"></a>**個々のファイルの共有スナップショットを作成できますか。**  
    共有スナップショットは、ファイル共有レベルで作成されます。 ファイル共有スナップショットから個々のファイルを復元できますが、ファイルレベルの共有スナップショットは作成できません。 ただし、共有レベルの共有スナップショットを取得して、特定のファイルが変更された共有スナップショットを一覧表示したい場合は、Windows にマウントされた共有で、**以前のバージョン**からこれを行うことができます。 
    
    ファイル スナップショット機能が必要な場合は、[Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) までお知らせください。

* <a id="encypted-snapshots"></a>**暗号化されたファイル共有の共有スナップショットを作成できますか。**  
    保存時の暗号化を有効にした Azure ファイル共有の共有スナップショットを取得できます。 共有スナップショットから暗号化されたファイル共有に、ファイルを復元できます。 お使いの共有が暗号化されている場合、共有スナップショットも暗号化されます。

* <a id="geo-redundant-snaphsots"></a>**使用している共有スナップショットは geo 冗長性でしょうか。**  
    共有スナップショットには、そのスナップショットが対象としている Azure ファイル共有と同じ冗長性があります。 お使いのアカウントに geo 冗長ストレージを選択した場合、共有スナップショットもペアになっているリージョンに冗長的に保存されます。

### <a name="manage-share-snapshots"></a>共有スナップショットを管理する
* <a id="browse-snapshots-linux"></a>**Linux から 共有スナップショットを参照できますか。**  
    Azure CLI 2.0 を使用して、Linux で共有スナップショットの作成、一覧表示、参照、復元が可能です。

* <a id="copy-snapshots-to-other-storage-account"></a>**共有スナップショットを別のストレージ アカウントにコピーできますか。**  
    共有スナップショットから別の場所にファイルをコピーできますが、共有スナップショット自体はコピーできません。

### <a name="restore-data-from-share-snapshots"></a>共有スナップショットからデータを復元する
* <a id="promote-share-snapshot"></a>**基本の共有に共有スナップショットを昇格させることはできますか。**  
    データは、共有スナップショットから他の宛先にコピーできます。 共有スナップショットを基本の共有に昇格させることはできません。

* <a id="restore-snapshotted-file-to-other-share"></a>**共有スナップショットから別のストレージ アカウントにデータを復元できますか。**  
    はい。 共有スナップショットのファイルは、元の場所にコピーできるほか、別の場所にコピーすることもできます。ストレージ アカウントまたはリージョンが同じでも異なっていてもかまいません。 また、オンプレミスの場所や他のクラウドにファイルをコピーすることもできます。    
  
### <a name="clean-up-share-snapshots"></a>共有スナップショットをクリーンアップする
* <a id="delete-share-keep-snapshots"></a>**共有スナップショットは削除せずに、共有を削除することはできますか。**  
    共有にアクティブな共有スナップショットがある場合、共有を削除することはできません。 API を使用して共有と一緒に共有スナップショットを削除することはできます。 共有スナップショットと共有の両方を削除するのであれば、Azure Portal で行うこともできます。

* <a id="delete-share-with-snapshots"></a>**ストレージ アカウントを削除した場合、共有スナップショットはどうなりますか。**  
    ストレージ アカウントを削除すると、共有スナップショットも削除されます。

## <a name="billing-and-pricing"></a>課金と価格
* <a id="vm-file-share-network-traffic"></a>**Azure VM と Azure ファイル共有の間のネットワーク トラフィックは、サブスクリプションに課金される外部帯域幅としてカウントされますか。**  
    ファイル共有と VM が同じ Azure リージョン内にある場合、そのファイル共有と VM の間のトラフィックに別途料金は発生しません。 ファイル共有と VM がそれぞれ異なるリージョンに存在する場合、両者の間のトラフィックは外部帯域幅として課金されます。

* <a id="share-snapshot-price"></a>**共有スナップショットにかかるコストを教えてください。**  
     共有スナップショットの容量に関して、プレビュー期間中は料金は発生しません。 Standard ストレージのエグレスとトランザクションのコストが適用されます。 一般公開後は、共有スナップショットの容量とトランザクションの料金が、サブスクリプションに対して課金されます。
     
     共有スナップショットは、本質的に増分です。 基本の共有スナップショットは、共有そのものです。 それ以降の共有スナップショットはすべて増分であり、先行する共有スナップショットとの差分のみを格納しています。 変更されたコンテンツに対してのみ、課金されます。 共有に 100 GiB のデータがあり、最新の共有スナップショットの後に 5 GiB だけが変更された場合、共有スナップショットは追加の 5 GiB のみを消費し、課金は 105 GiB に対して行われます。 トランザクションおよび標準エグレスの課金の詳細については、[課金に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)をご覧ください。

## <a name="scale-and-performance"></a>スケールとパフォーマンス
* <a id="files-scale-limits"></a>**Azure Files のスケールの上限を教えてください。**  
    Azure Files のスケーラビリティおよびパフォーマンスのターゲットについては、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](storage-files-scale-targets.md)」を参照してください。

* <a id="need-larger-share"></a>**Azure Files で現在提供されているよりも大きなファイル共有を必要としています。Azure ファイル共有のサイズを増やすことはできますか。**  
    
いいえ。 Azure ファイル共有の最大サイズは、5 TiB です。 これは、現時点でのハード制限であり、調整することはできません。 現在、共有サイズを 100 TiB にまで増やすソリューションに取り組んでいることろですが、現時点でスケジュールをお伝えすることはできません。

* <a id="open-handles-quota"></a>**同じファイルに同時にアクセスできるクライアントの数はどのくらいですか。**   
    開くことができるハンドルのクォータは、1 つのファイルにつき 2,000 個です。 開いているハンドルが 2,000 個になると、クォータに達したことを伝えるエラー メッセージが表示されます。

* <a id="zip-slow-performance"></a>**Azure Files にファイルを解凍する際にパフォーマンスが低かった場合は、どうすればよいですか。**  
    Azure Files に大量のファイルを転送する場合、AzCopy (Windows 用。Linux/Unix 用はプレビュー) または Azure PowerShell を使用することをお勧めします。 これらのツールは、ネットワーク転送に最適化されています。

* <a id="slow-perf-windows-81-2012r2"></a>**Windows Server 2012 R2 または Windows 8.1 で Azure ファイル共有をマウントしました。パフォーマンスが低下していますが、なぜでしょうか。**  
    Windows Server 2012 R2 および Windows 8.1 で Azure ファイル共有をマウントした場合、問題が生じることが確認されています。 この問題は、2014 年 4 月の Windows 8.1 および Windows Server 2012 R2 向けの累積更新プログラムのパッチで解決されています。 パフォーマンスを最適化するために、Windows Server 2012 R2 および Windows 8.1 のすべてのインスタンスに、このパッチが適用されていることを確認してください  (Windows Update で常に Windows パッチを取得することが推奨されます)。詳細については、関連する Microsoft サポート技術情報の記事「[Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2 (Windows 8.1 または Server 2012 R2 から Azure Files にアクセスするときにパフォーマンスが低下する)](https://support.microsoft.com/kb/3114025)」を参照してください。

## <a name="features-and-interoperability-with-other-services"></a>機能およびその他のサービスとの相互運用性
* <a id="cluster-witness"></a>**Azure ファイル共有を Windows Server フェールオーバー クラスターの "*監視用ファイル共有*" として使用できますか。**  
    その構成は現在、Azure ファイル共有ではサポートされていません。 Azure Blob Storage 用にこれを設定する方法の詳細については、「[Deploy a Cloud Witness for a Failover Cluster (フェールオーバー クラスターのクラスター監視のデプロイ)](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)」をご覧ください。

* <a id="containers"></a>**Azure Container Instances で Azure ファイル共有をマウントできますか。**  
    はい。Azure ファイル共有は、コンテナー インスタンスの有効期間が過ぎた後も情報を保持する方法として最適です。 詳細については、「[Azure Container Instances での Azure ファイル共有のマウント](../../container-instances/container-instances-mounting-azure-files-volume.md)」を参照してください。

* <a id="rest-rename"></a>**REST API に名前変更の操作はありますか。**  
    現時点ではありません。

* <a id="nested-shares"></a>**共有は入れ子にできますか。つまり、共有の下に共有を配置できますか。**  
    
いいえ。 ファイル共有はマウント可能な "*仮想ドライバーである*" ため、共有の入れ子はサポートしていません。

* <a id="ibm-mq"></a>**IBM MQ での Azure Files の使用方法**  
    IBM MQ ユーザーが IBM のサービスで Azure Files を構成する際に役立つドキュメントが IBM から提供されています。 詳細については、「[How to set up IBM MQ Multi instance queue manager with Microsoft Azure Files Service (IBM MQ マルチ インスタンス キュー マネージャーを Microsoft Azure Files サービスで設定する方法)](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)」を参照してください。

## <a name="see-also"></a>関連項目
* [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure ファイル同期のトラブルシューティング (プレビュー)](storage-sync-files-troubleshoot.md)
