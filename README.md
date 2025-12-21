# Server Control for Minecraft
スマホからワンタップでサーバーを操作できるマイクラサーバー構築・運用システム

## 📖 概要 (Overview)
技術に詳しくない方でも、**「月額ワンコイン（約400円）」**で安全にMinecraftサーバーを運用できるように開発した、スマホアプリとAWSインフラのパッケージです。

「専用サーバーを作りたいが、月額式のレンタルサーバーは高いし、自分でサーバーを構築するのは難しすぎる」という方の課題をAWSとReact Nativeを用いて解決しました。

## 🏗️ アーキテクチャ (Architecture)
<img width="1768" height="487" alt="image" src="https://github.com/user-attachments/assets/ce94953e-25dd-481a-9911-3093a97629ad" />

### 採用技術スタック
* **Frontend:** React Native (Expo)
* **Backend:** AWS Lambda (Node.js), Amazon API Gateway
* **Infrastructure:** Amazon EC2 (Ubuntu 24.04), Amazon EBS (gp3)
* **IaC:** AWS CloudFormation
* **Management:** AWS Systems Manager (SSM), Amazon CloudWatch
* **Storage:** Amazon S3 (Template Hosting)

## 💡 こだわり・工夫した点 (Highlights)

### 1. 徹底的なコスト最適化 (Cost Optimization)
「週末しか遊ばない」という利用実態に合わせ、常時稼働ではなくオンデマンド構成を採用しました。
* **自動停止機能:** CloudWatch Alarmにより、CPU使用率が低い状態（誰も遊んでいない状態）が30分続くと自動でサーバーを停止（Stop）。消し忘れによる高額請求を物理的に防いでいます。
* **試算:** 週末10時間の利用で、月額約 $2.70（約400円）で運用可能です。

### 2. AWSに詳しくない方にも優しいUX設計 (User Experience)
インフラ構築のハードルを極限まで下げるため、**CloudFormation Quick Create Link（魔法のリンク）** を実装しました。
* AWSアカウント作成後、URLをクリックし、パスワードを決めるだけ。S3へのテンプレートアップロードや、パラメータの複雑な設定は一切不要です。
* **アプリ内バッジ通知:** サーバーのアップデートが必要な際は、アプリ起動時にGitHub上のバージョン定義を参照し、「🔴」バッジでお知らせします。

### 3. セキュアな運用設計 (Security & Operations)
個人開発ですが、企業のセキュリティ基準を意識した設計を行いました。
* **SSHポート(22)の閉鎖:** メンテナンスは全て AWS Systems Manager (Session Manager / Run Command) 経由で行うため、攻撃の穴となる22番ポートは開放していません。
* **最小権限の原則 (Least Privilege):** LambdaのIAMロールは、操作対象のEC2リソースと実行するSSMドキュメントのみに権限を絞り込み、ワイルドカード(`*`)の乱用を避けています。
* **OS更新の自動化:** ユーザーが「マイクラの更新」ボタンを押した際、裏側で `apt-get upgrade` も同時に実行されるスクリプトを組み込み、意識せずともOSのセキュリティパッチが適用されるようにしています。

## 🤖 生成AIの活用について (Utilization of Gen-AI)
本プロジェクトは、**「メインフレームエンジニアからのスキルチェンジ」** を目的として、モダン技術の習得と開発サイクルの高速化のために生成AI (Gemini) を積極的に活用しました。

* **役割分担:**
    * **私 (Architect):** 要件定義、AWSアーキテクチャ設計、セキュリティポリシーの策定（IAM権限の分離など）、コスト試算、UX設計。
    * **AI (Copilot):** CloudFormation (YAML) のドラフト作成、React NativeのUIコンポーネント実装、エラーログの解析。
* **成果:**
    * 未経験のReact NativeとIaC実装を含め、**実質1ヶ月（週末のみ）** でリリース品質まで到達しました。AIが生成したコードは全て自身でレビューし、特にIAM権限や課金リスクに関する部分は厳密に修正を行っています。

## 🚀 使い方 (Getting Started)

1. **AWS環境の構築**
   以下のリンクをクリックし、CloudFormationスタックを作成してください。
   [⚡ サーバー構築はこちら (Quick Create Link)](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/quickcreate?stackName=MinecraftServer&templateURL=https://minecraft-template-server-control.s3.ap-northeast-1.amazonaws.com/minecraft.yaml
)

2. **アプリの設定**
   スタック作成後に表示される `ApiUrl` をアプリに入力してください。

## 🛠️ 今後の展望 (Future Roadmap)
* 複数のワールドデータのバックアップと切り替え機能（S3連携）。
* アドオンをスマホから追加できる機能
