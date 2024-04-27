---
title: "4章"
---

# 4章: API リソースと kubectl

## 4.2: Kubernetesの基礎

- k8sの構成
  - k8s master
    - APIエンドポイントの提供
      - kubectlで操作
        - ※REST APIで実装されているので直接操作も可能
    - コンテナのスケジューリング
    - コンテナのスケーリング
  - k8s node
    - Dockerホストに相当
- k8sのクラスタ操作
  - マニフェストファイル(yaml or json)+kubectlでmasterにリソースの登録

## 4.3: リソース

- リソース
  - コンテナの実行
  - ロードバランサの作成
- 大きく分けて5つ
  - Workloads API
    - コンテナ実行(5章)
      - Pod
      - ReplicationController
      - ReplicaSet
      - Deployment
      - DaemonSet
      - StatefulSet
      - Job
      - CronJob
  - Service API(6章)
    - コンテナ外部公開のエンドポイント提供
      - Service
        - ClusterIP
        - ExternalIP
        - NodePort
        - LoadBalancer
        - Headless
        - ExternalName
        - None-Selector
      - Ingress
  - Config and Storage API(7章)
    - 設定/機密情報/永続化ボリューム
      - Secret
      - ConfigMap
      - PersistentVolumeClaim
  - Cluster API(8章)
    - セキュリティ/クォータ
      - Node
      - Namespace
      - PersistentVolume
      - ResourceQuota
      - ServiceAccount
      - Role
      - ClusterRole
      - RoleBinding
      - ClusterRoleBinding
      - NetworkPolicy
  - Metadata API(8章)
    - クラスタ内の他のリソース操作
      - LimitRange
      - HorizontalPodAutoscaler
      - PodDistruptionBudget
      - CustomResourceDefinition

## 4.4: Namespaceによる仮想的なクラスタの分離

- 仮想的なk8sクラスタの分離機能
  - 完全な分離レベルではない
  - 1つのk8sクラスタを複数チームで利用
  - プロダクション/ステージング/開発などの環境ごとの分割
- 初期状態は4つ
  - kube-system
  - kube-public
  - kube-node-lease
  - default ←ここに最初は格納される
- マネージドサービスを使うとより高いレベルでの分離が可能
  - RBAC(Role Based Access Control)
  - Network Policy
  - これら2つを用いて分離性を高める
    - アプリ開発者はdefault, kube-systemなどのNamespaceに入れないようにする
    - アプリ開発者は自身のマイクロサービスのNamespaceに入れる
    - 一般ユーザはdev環境にしか入れない
- Namespaceはマイクロサービスを開発するチームごとにすべき
  - プロダクション/ステージング/開発に関してはNamespaceではなくクラスタごと分けるべき

## 4.5: kubectl

- 認証情報とContext
  - kubectlがmasterと通信するときは、接続先サーバの情報や認証情報が必要
    - kubeconfig(デフォルト: ~/.kube/config)を参照する
      - 複数の対象を登録可能
      - kubectlコマンドで登録･切り替え
      - kubectlの代わりに、OSSのkubectx, kubensなども有効(冗長ではない、補完が効く)
- リソース作成
  - createよりapplyのほうが使いやすい
    - applyに統一できる
    - metadataでの差分確認がしやすい
  - waitで待機可能
- マニフェストファイル
  - 単一ファイル: apply -f [FILE_NAME]
  - 複数ファイル: apply -f [DIR_NAME] -R(再帰オプション)
- アノテーション
  - システムコンポーネントが利用するメタデータ
  - metadata.annotation
  - すべての環境で利用できない情報を付与
    - システムコンポーネントが勝手に利用
    - 特定のマネージドサービスのみの利用
    - β版機能の設定
    - メモとしての利用
- ラベル
  - リソースの管理に利用するメタデータ
  - metadata.labels
  - リソースの管理に利用
    - 開発者向け
      - リソースのフィルタリング
    - システム向け
      - レプリカ数管理
      - ロードバランサからトラフィックを流すpod選択
      - 衝突しないような設計が必要
- その他コマンド多数
