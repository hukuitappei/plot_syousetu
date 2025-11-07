flowchart TD
    Start([チャットボット起動]) --> Login[ログイン認証]
    Login -->|認証成功| SessionInit[セッション管理初期化]
    Login -->|認証失敗| LoginError[認証エラー]
    LoginError --> End1([終了])
    
    SessionInit --> WaitQuestion[質問受付待機]
    WaitQuestion --> ReceiveQuestion[ユーザー質問受付]
    
    ReceiveQuestion --> PreProcess[前処理開始]
    PreProcess --> LLMSummary[LLM要約]
    LLMSummary --> LLMTag[LLM自動タグ付け<br/>基板カテゴリ/機種カテゴリ/関連性]
    
    LLMSummary -->|LLM失敗| ErrorFlow[定型質問フロー]
    LLMTag -->|LLM失敗| ErrorFlow
    ErrorFlow --> WaitQuestion
    
    LLMTag --> VectorSearch[ベクトルDB意味的検索]
    VectorSearch --> FilterCheck{検索結果あり?}
    
    FilterCheck -->|0件| NoResult[問い合わせ先表示<br/>福井宛]
    NoResult --> WaitQuestion
    
    FilterCheck -->|1件以上| TagFilter[タグフィルタリング<br/>RDBでタグ管理]
    TagFilter --> FilterResult{フィルタリング後<br/>結果あり?}
    
    FilterResult -->|0件| NoResult
    FilterResult -->|1件以上| AnswerCheck{回答抽出可能?}
    
    AnswerCheck -->|直接抽出可能| ExtractAnswer[回答抽出]
    AnswerCheck -->|1件以上| SummarizeAnswer[回答要約]
    
    ExtractAnswer --> CheckImage{画像あり?}
    SummarizeAnswer --> CheckImage
    
    CheckImage -->|あり| DisplayWithImage[回答表示<br/>テキスト+画像]
    CheckImage -->|なし| DisplayText[回答表示<br/>テキストのみ]
    
    DisplayWithImage --> Feedback[状況更新待機]
    DisplayText --> Feedback
    
    Feedback --> AutoFeedback{自動判定可能?}
    AutoFeedback -->|改善した| UpdateSession1[セッション更新<br/>実施済み項目リスト追加]
    AutoFeedback -->|変化なし| UpdateSession2[セッション更新<br/>実施済み項目リスト追加]
    AutoFeedback -->|症状変化| UpdateSession3[セッション更新<br/>実施済み項目リスト追加]
    AutoFeedback -->|そのほか| UserInput[ユーザー入力待機]
    
    UserInput --> UpdateSession4[セッション更新<br/>実施済み項目リスト追加]
    UpdateSession1 --> SessionUpdate[セッション管理更新]
    UpdateSession2 --> SessionUpdate
    UpdateSession3 --> SessionUpdate
    UpdateSession4 --> SessionUpdate
    
    SessionUpdate --> PriorityAdjust[優先度調整<br/>実施済み項目の優先度を下げる]
    PriorityAdjust --> TagManagement[タグ管理<br/>基板タグへの作業手順管理]
    TagManagement --> WaitQuestion
    
    style Start fill:#e1f5ff
    style End1 fill:#ffe1e1
    style Login fill:#fff4e1
    style LLMSummary fill:#e1ffe1
    style LLMTag fill:#e1ffe1
    style VectorSearch fill:#e1e1ff
    style TagFilter fill:#e1e1ff
    style ExtractAnswer fill:#ffe1ff
    style SummarizeAnswer fill:#ffe1ff
    style DisplayWithImage fill:#ffffe1
    style DisplayText fill:#ffffe1
    style SessionUpdate fill:#f0e1ff
    style ErrorFlow fill:#ffe1e1
    style NoResult fill:#ffe1e1

