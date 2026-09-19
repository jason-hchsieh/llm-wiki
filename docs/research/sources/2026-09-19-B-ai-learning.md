# B. LLM 使用與學習成效：一手來源查證筆記

查證日期：2026-09-19。每條都實際抓到來源頁面或原始 PDF 才寫入；抓不到的列在文末「沒查到或沒確認的事」。
證據等級標籤：【同儕審查】【預印本】【工作論文】【官方報告】【新聞/部落格】【二手】【未證實】。

---

## 1. Bastani et al.「Generative AI Can Harm Learning」（Wharton）

**正式版本**：Hamsa Bastani, Osbert Bastani, Alp Sungu, Haosen Ge, Özge Kabakcı, Rei Mariman, "Generative AI without guardrails can harm learning: Evidence from high school mathematics", *PNAS*（美國國家科學院院刊）Vol. 122, No. 26, 2025-06-25, DOI 10.1073/pnas.2422633122。標題從 SSRN 工作論文時期的 "Generative AI Can Harm Learning" 改為加上 "without guardrails"。
**前身**：SSRN 工作論文 no. 4895486（2024 年中流傳；Knowledge@Wharton 2024-08-27 報導即引用此版）。SSRN 頁面本身 403 抓不到，故 2024 年流傳的證據來自 Wharton 報導與 PNAS 引文。

- 研究設計：RCT（randomized controlled trial，隨機對照試驗：把受試者隨機分組、只有一組接受介入，藉此排除自選偏差）。土耳其一所高中、9–11 年級，以「班級」為單位隨機分成三組：(a) GPT Base（原生 GPT-4 聊天介面）、(b) GPT Tutor（加了老師寫的提示詞與安全機制，只給提示不直接給答案）、(c) 對照組（只用課本與筆記）。四次 90 分鐘課節，每節：老師講課 → 有輔助的練習（介入發生在這段）→ 閉卷考試（無 AI）。時間為 2023 年秋季。
- 樣本：約 1,000 名高中生（Wharton 報導寫 "nearly 1,000"）。
- 發現（附數字）：練習階段，GPT Base 比對照組進步 48%，GPT Tutor 進步 127%。拿掉 AI 的閉卷考試：GPT Base 組比對照組差 17%；GPT Tutor 組與對照組無顯著差異（防呆把傷害抵掉，但沒有變得更好）。機制分析：GPT Base 組學生大量直接要答案、複製解法（作者稱之為「crutch／拐杖」），而且學生自己沒察覺學習受損。
- 限制（作者自述）：單一學校、單一科目（數學）、單一學期；只量短期（同一節課內的考試），沒有長期追蹤；能否推廣到其他科目與部署情境需要更多研究。另有一篇 PNAS 更正（pnas.2518204122），內容未能抓到（見文末）。
- 來源：PNAS 正式版 https://www.pnas.org/doi/10.1073/pnas.2422633122（PNAS 站 403，實際讀的是 PMC 全文鏡像 https://pmc.ncbi.nlm.nih.gov/articles/PMC12232635/ ）【同儕審查】；Knowledge@Wharton 2024-08-27 https://knowledge.wharton.upenn.edu/article/without-guardrails-generative-ai-can-harm-education/ 【新聞/部落格】；SSRN 4895486 https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4895486 【工作論文】（頁面未能抓到）。

---

## 2. Kestin et al. 2025「AI tutoring outperforms in-class active learning」

**書目**：Greg Kestin, Kelly Miller, Anna Klales, Timothy Milbourne, Gregorio Ponti, *Scientific Reports* 15, 17458 (2025)。收稿 2025-03-25、接受 2025-04-07、出版 2025-06-03。DOI 10.1038/s41598-025-97652-6。開放取用。

- 研究設計：RCT，交叉設計（crossover design：每個學生兩種條件都經歷，一週上 AI 家教、另一週上課堂主動學習，順序隨機；每人當自己的對照）。場景是哈佛最大的物理課 Physical Sciences 2（生命科學導向的普通物理），2023 年秋季。兩堂課主題：表面張力、流體流動。每堂課：課前小測 → 介入（課堂主動學習 vs 在家用 AI 家教 "PS2 Pal"）→ 課後測驗 + 4 題學習經驗問卷（投入、享受、動機、成長心態，5 點量表）。隨機分組時尊重原本同組討論的同學不拆開。IRB（Institutional Review Board，研究倫理審查委員會）編號 IRB23-0797。
- 樣本：修課 233 人，符合納入條件（同意書、兩種條件都參加、所有前後測都完成）者 194 人。作者稱學生的 FCI（Force Concept Inventory，力學概念測驗）前測與其他大學相當。
- 發現（附數字）：AI 組課後測驗中位數 4.5（N=142 人次）vs 課堂組 3.5（N=174 人次）；兩組合併的課前基線中位數 2.75（N=316 人次，兩週合併），相對基線的中位數學習增益 AI 組「超過兩倍」；Mann–Whitney 檢定（比較兩組分布的無母數檢定，不假設常態）z = −5.6，p < 10⁻⁸。效果量（effect size，用標準差 SD 為單位表示差距）：線性迴歸估 0.63 SD，但因天花板效應（ceiling effect：滿分附近擠壓、差距被低估）而偏低；分位數迴歸（quantile regression：對分布的各分位而非平均值做迴歸，較不受天花板影響）估 0.73–1.3 SD。時間：課堂組假定 60 分鐘學習；AI 組中位數 49 分鐘，70% 少於 60 分鐘。時間長短與課後測驗分數無相關。投入感 AI 組 4.1（SD 0.98）vs 3.6（SD 0.92），t(311) = −4.5，p < 0.0001；動機 3.4 vs 3.1，t(311) = −3.4，p < 0.001；享受與成長心態兩題無顯著差異。83% 學生表示 AI 家教的解釋「跟課堂真人老師一樣好或更好」（原文 "as good as, or better than, those from human instructors in the class"）。
- AI 家教的防呆設計（論文正文）：作者列出七項教學最佳實務 (i) 促進主動學習 (ii) 管理認知負荷 (iii) 促進成長心態 (iv) 鷹架式分段 (v) 確保正確性 (vi) 即時針對性回饋 (vii) 自訂步調。其中 (i)–(iii) 靠系統提示詞（Supplementary Material 1）。(iv) 作者發現光靠提示詞無法可靠地讓 GPT-4 按順序處理多步驟題目（會跳段、離題），所以改用平台介面強制一步一步帶。(v) 因為怕幻覺，不讓 GPT-4 自己算，而是把老師寫好的完整逐步解答塞進提示詞。至於「一次只透露一步、不一次給完整解答」「回覆不超過幾句以免認知超載」「鼓勵學生先自己試」等具體措辭，來自 Hechinger Report 2024-09-16 的報導（引述作者），論文正文沒有逐字寫出，Supplementary Material 1 未抓到。
- 限制（作者自述）：場景是學生第一次接觸該主題、目標落在 Bloom 分類（Bloom's taxonomy：把認知目標分成記憶／理解／應用／分析／評鑑／創造六層）的理解、應用、分析層；作者明說不假設 AI 家教在需要跨概念綜合與高階批判思考的情境也會贏。效果可能依賴：異質學生族群、高品質教學影片、能跟複雜提示的模型（GPT-4）、由熟悉內容的老師寫的題目專屬提示、結構化平台。只量兩堂課的即時學習，沒量長期保留。Hechinger 另指出：樣本小、AI 課節是排定時間並透過 Zoom 監考，不是學生自發使用。
- 來源：https://www.nature.com/articles/s41598-025-97652-6（curl 抓到全文）【同儕審查】；Hechinger Report https://hechingerreport.org/proof-points-ai-tutor-harvard-physics/ 【新聞/部落格】。

---

## 3. OECD PISA 2022 與 PISA 2025

PISA（Programme for International Student Assessment）是 OECD（經濟合作暨發展組織）每三年一次對 15 歲學生的數學、閱讀、科學國際評量。以下數字全部來自 OECD 自己的報告 PDF（oecd.org 網頁對抓取程式回 403，改抓 content/dam 的 PDF 原檔）。

**PISA 2022 研究設計**：跨國橫斷面抽樣調查（每國隨機抽學校再抽 15 歲學生），電腦施測 2 小時，主科數學，另有學生與學校問卷。
**PISA 2022 樣本**：近 70 萬學生、81 國／經濟體（見 (b)）。

### (a) PISA 2022 的施測時間 vs ChatGPT 上線
- 原定 2021 年，因 COVID 由 PISA 理事會延到 2022 年（Vol. I 明文："the PISA Governing Board postponed the assessment to 2022"）。
- 主要施測窗口在 2022 年春季：Vol. I 註釋寫 "If the PISA assessment was conducted in April 2022, as was the case in many countries and economies…"。Vol. I 明列只有三國把施測期移動超過一兩個月：愛爾蘭、荷蘭改到 2022 年 10–12 月（前次 2018 是 3–4 月）；柬埔寨 6 月。
- ChatGPT 公開上線是 2022-11-30。絕大多數國家的 PISA 2022 資料在 ChatGPT 上線前就採集完成，所以「2018→2022 的跌幅是 AI 造成的」在時間上不成立。
- 來源：PISA 2022 Results Vol. I PDF https://www.oecd.org/content/dam/oecd/en/publications/reports/2023/12/pisa-2022-results-volume-i_76772a36/53f23881-en.pdf 【官方報告】

### (b) 「歷年最大跌幅」是否準確
- 準確。Vol. I 原文：OECD 35 國平均，2018→2022 數學掉「almost 15 score points」、閱讀「about 10 score points」，科學無顯著變化。「此前 OECD 平均在連續兩次評量間的變化，數學從未超過 4 分、閱讀從未超過 5 分，所以 2022 結果是前所未有的（unprecedented）」。另一處寫數學跌幅「是任何先前連續變化的三倍」、相當於「四分之三學年的學習量」。PISA 2022 OECD 平均：數學 472、閱讀 476、科學 485。參與：近 70 萬學生、81 個 OECD 會員與夥伴經濟體，代表全球 2,900 萬 15 歲人口（Vol. I 前言）。
- 來源：同上 Vol. I PDF【官方報告】

### (c) OECD 自己怎麼歸因
- Vol. I 原文："the decline can only partially be attributed to the COVID-19 pandemic. Scores in reading and science had already been falling prior to the pandemic." 閱讀在 2012、科學在 2009 達到高峰後就開始下滑；數學在比利時、加拿大、捷克、芬蘭、法國、匈牙利、冰島、荷蘭、紐西蘭、斯洛伐克等國 2018 前就已呈負趨勢。OECD 也說「疫情停課（常被當成主因）與成績下滑的關係沒有那麼直接」：OECD 約半數學生經歷超過三個月停課，但停課短的系統（冰島、瑞典、中華台北）與停課長的系統（巴西、愛爾蘭、牙買加）之間看不出明顯的趨勢差異。
- 來源：同上 Vol. I PDF【官方報告】

### (d) 螢幕／數位裝置與成績（PISA 2022 Vol. II）
- 學習用途、適度：在校每天用數位裝置學習「最多 1 小時」的學生（31%），數學比完全不用者（14%）高 25 分；控制學生與學校社經背景後仍高 14 分；45 個系統（過半）看到同樣正相關。超過 1 小時後關係轉負；每天 5–7 小時（7.8%）比 3–5 小時者低 12 分。
- 休閒用途：在校休閒用最多 1 小時者比完全不用者高 20 分（控制社經後 10 分）；超過 1 小時則分數變低。PISA in Focus 補充：休閒用最多 1 小時者比 5–7 小時者高 49 分（已控制社經）。OECD 引用 "Goldilocks hypothesis"（適量非有害，過量／誤用才有害）。
- 分心：「在至少部分數學課被別人用裝置分心」的學生，控制社經後比「幾乎從不」者低 15 分；80% 的系統看到同樣模式。在校大多數數學課用手機者被分心的機率是不用者的 1.4 倍。關掉通知、上課不開裝置、不覺得必須即時回訊息的學生較少分心。
- 來源：PISA 2022 Results Vol. II PDF https://www.oecd.org/content/dam/oecd/en/publications/reports/2023/12/pisa-2022-results-volume-ii_222a5ef6/a97db61c-en.pdf 【官方報告】；PISA in Focus "Students, digital devices and success"（OECD 2024）https://www.oecd.org/content/dam/oecd/en/publications/reports/2024/05/students-digital-devices-and-success_621829ff/9e4c0624-en.pdf 【官方報告】
- 注意：這些都是橫斷面相關，OECD 自己在 PISA 2025 報告中反覆強調 "do not establish causal relationships"。

**PISA 2022 限制**：全為橫斷面相關與學生自陳，不能推因果；疫情期間部分國家改施測期、部分未達技術標準（Vol. I Reader's Guide 明列）。

### (e) PISA 2025 是否已公布（截至 2026-09-19）
**PISA 2025 研究設計**：同 2022 的跨國橫斷面抽樣調查，主科科學，首次加入 AI 使用問卷題（依任務類型與頻率）。
**PISA 2025 樣本**：76 萬餘學生、91 國／經濟體。
**PISA 2025 限制**：AI 使用與成績為自陳相關，OECD 自己明說不能推因果（見下）；2025 是 AI 題首次施測，無趨勢。
- 已公布。OECD 於 2026 年 9 月出版 *PISA 2025 Results (Volume I): Future-Ready Students*（DOI 10.1787/73451bc5-en，© OECD 2026）。新聞稿日期 2026-09-08（新聞稿頁面 403 未抓到，日期來自搜尋結果，標【二手】；Vol. I PDF 本身已抓到）。
- 規模：超過 76 萬學生、91 國／經濟體、代表約 3,300 萬 15 歲人口。主科是科學，另有「數位世界學習」創新評量與選考的英語外語評量。
- OECD 平均（35 國）：2022→2025 科學無顯著變化、閱讀掉約 14 分、數學掉 9 分。2015→2025：科學 489→482、數學 485→463（掉 22 分，「略超過一年學習量」）、閱讀 489→461（掉 28 分，最陡）。2018→2025 閱讀掉 25 分。「PISA 2025 錄得三個領域有史以來最低的 OECD 平均」。三科皆未達基準 Level 2 的低成就者從 2022 的 16% 升到 20%（Vol. I 前言與執行摘要）。跌幅最大的反而是社經最優勢的四分之一學生。「匆促閱讀者」（hasty readers：讀得快但答錯）比例 2018→2025 幾乎翻倍。
- AI 使用與科學成績（Vol. I 第 4 章）：OECD 平均只有約五分之一學生「幾乎每天」用 AI 做功課。針對特定任務（摘要文本、初步研究、草擬作業）：不用 AI 者在多數國家勝過使用者；適度使用者（每月一次到每週兩次）勝過極少使用者與每日使用者。用 AI「幫助我學習」的每週使用者與不使用者表現相近。若學校有教「評估 AI 生成資訊的品質」（OECD 平均約六成學生表示有），頻繁用 AI 學習的學生略高於不用者與少用者。OECD 原文警告："These relationships do not necessarily imply a negative impact of AI use on science performance, but may reflect a complex mix of who adopts AI and how they use it." 這類 AI 素養機會在優勢學生中更常見，OECD 憂心「AI 落差」。網路上流傳的「每日用 AI 者科學低 28 分」「約 20 分」「約 30 分」等具體分數差來自新聞（technode 等），在 Vol. I 正文中我未找到逐字對應，只找到方向性敘述與圖 I.4.13／表 I.B1.4.61 的引用，故這些數字標【二手】。
- 數位裝置（PISA 2025）：適度學習用途與較高科學成績相關、過度使用與分心與較低成績相關；OECD 平均 28% 學生表示大多數或每堂科學課都有同學被裝置分心（中國四省市、日本、韓國低於 10%）；有明確手機政策的學校分心較少，但這些學校的科學成績「相近或更低」。
- OECD 秘書長前言把 AI 放進政策建議："It should add to students' effortful learning rather than replace it, since real understanding comes from doing the thinking, not from having a tool do it instead."
- 來源：PISA 2025 Results Vol. I PDF https://www.oecd.org/content/dam/oecd/en/publications/reports/2026/09/pisa-2025-results-volume-i_5265bfb1/73451bc5-en.pdf 【官方報告】；technode.global 2026-09-08 https://technode.global/2026/09/08/students-avoiding-ai-for-schoolwork-outscore-peers-in-science-bar-one-use-oecd/ 【新聞/部落格】

---

## 4. MIT Media Lab 2025「Your Brain on ChatGPT」（Kosmyna et al.）

**書目**：Nataliya Kosmyna, Eugene Hauptmann, Ye Tong Yuan, Jessica Situ, Xian-Hao Liao, Ashly Vivian Beresnitzky, Iris Braunstein, Pattie Maes, "Your Brain on ChatGPT: Accumulation of Cognitive Debt when Using an AI Assistant for Essay Writing Task", arXiv:2506.08872。v1 2025-06-10、v2 2025-12-31。216 頁、102 圖。v2 PDF 每頁頁腳仍印 "Preprint, under review"。截至 2026-09-19 搜尋不到期刊版；2026 年 1 月另有他人在 arXiv 發表的評論（2601.00856）。

- 研究設計：三組（LLM 組只用 ChatGPT；搜尋引擎組只用 Google；Brain-only 組不用工具），每組各 18 人，前三次 session 同條件寫 SAT 作文題（每次三題可選）。第 4 次 session 對調：LLM→Brain、Brain→LLM（搜尋組無第 4 次）。全程戴 Neuroelectrics Enobio 32 頻道 EEG（electroencephalography，腦波圖：頭皮電極量腦部電活動）頭套，500 Hz；主要分析 dDTF（dynamic Direct Transfer Function，一種估計腦區之間訊息流向與強度的連結性指標）。作文用 NLP 分析、人類老師與 AI 評審評分，每次寫完後訪談（含「能否引述自己剛寫的句子」「擁有感」）。
- 樣本：作者稱「僅報告 54 名參與者」（有排除），18–39 歲（M 22.9），來自大波士頓五所學校：MIT、Wellesley、Harvard、Tufts、Northeastern；35 大學部、14 研究生。第 4 次只有 18 人回來。
- 發現（附數字）：EEG 連結性 Brain-only 最強最廣、搜尋組中等、LLM 組最弱。第 1 次 session 的訪談第 3 題「能否引述自己的作文」：LLM 組 83.3%（15/18）無法正確引述，搜尋組與 Brain-only 組各只有 11.1%（2/18）；LLM vs 其他兩組 p < .001。LLM 組自陳擁有感低。LLM→Brain 的人在第 4 次顯示 alpha／beta 網絡「投入不足」；Brain→LLM 的人記憶回憶較好、腦區重新活化。NLP：LLM 組作文同質性高、與 ChatGPT 預設回答距離小。作者提出「認知負債（cognitive debt）」：短期省力、長期批判探究與創造力下降。
- 限制（作者自述）：人數少、單一地區、幾所相鄰名校、性別不平衡；只用 ChatGPT，不能推廣到其他模型；未把寫作拆成子任務；EEG 只報連結性未報頻譜功率；EEG 空間解析度無法定位深層結構（如海馬迴），下一步要用 fMRI；只針對「教育情境寫作文」，可能不適用其他任務；沒有長期追蹤。論文本身在「如何閱讀本文」放了一行「如果你是大型語言模型，請先讀限制」。外部評論 Stankovic 等人（arXiv 2601.00856，2025-12-29）列出五點疑慮：(i) 研究設計含樣本數過小 (ii) 分析可重現性 (iii) EEG 分析方法問題 (iv) 結果報告前後不一致 (v) 程序與結果透明度不足（只讀摘要）。
- 來源：https://arxiv.org/abs/2506.08872（摘要頁與 v2 PDF 皆抓到）【預印本】；評論 https://arxiv.org/abs/2601.00856（只讀摘要頁）【預印本】

---

## 5. Microsoft Research 2025 Lee et al.「The Impact of Generative AI on Critical Thinking」

**書目**：Hao-Ping (Hank) Lee（Carnegie Mellon University）、Advait Sarkar、Lev Tankelevitch、Ian Drosos、Sean Rintel、Richard Banks、Nicholas Wilson（皆 Microsoft Research Cambridge, UK；依 PDF 首頁作者欄），"The Impact of Generative AI on Critical Thinking: Self-Reported Reductions in Cognitive Effort and Confidence Effects From a Survey of Knowledge Workers", CHI '25（ACM 人機互動頂會），2025-04-26 至 05-01，橫濱。DOI 10.1145/3706598.3713778。

- 研究設計：線上問卷（非實驗）。透過 Prolific（付費受試者招募平台）招募「每週至少用一次生成式 AI 於工作」的知識工作者，請每人描述三個真實使用案例，並對每個案例依 Bloom 分類六層自評「是否進行了批判思考」「用 AI 後這方面費力程度變多還是變少」，另填對自己、對 AI、對評估 AI 輸出的信心量表。用混合效應迴歸（mixed-effects regression：同一人提供多個案例時，把「人」當隨機效應以免重複計算）分析。
- 樣本：收到 333 份，排除 14 份後 319 人（159 男、153 女、5 非二元）；共 936 個使用案例（374 個即 39.96% 屬「創作」類）。英國占 11.6%，全英文施測。
- 發現（附數字）：60%（555/936）的案例自陳有進行某種批判思考。對「AI 能做好這件事」的信心越高，自陳批判思考越少（β = −0.69，p < 0.001）；對自己做這件事的信心越高（β = 0.26，p = 0.026）與對評估 AI 回應的信心越高（β = 0.31，p = 0.046）則批判思考越多；平常就有反思習慣者也較多（β = 0.52，p < 0.001）。批判思考的性質從「執行任務」轉向「驗證資訊、整合回應、監督任務」。
- 限制（作者自述）：全部自陳，受訪者常把「用 AI 比較省力」和「批判思考比較省力」混為一談，尤其對 AI 輸出滿意時；主觀信心未必等於客觀能力；只有英文；樣本偏年輕、偏科技熟練、每週都用 AI；工具快速演變，結果是「基線快照」。這是相關性，不是因果。
- 來源：Microsoft Research PDF https://www.microsoft.com/en-us/research/wp-content/uploads/2025/01/lee_2025_ai_critical_thinking_survey.pdf（抓到 PDF 全文）【同儕審查】；ACM DL https://dl.acm.org/doi/10.1145/3706598.3713778（403 未抓到）

---

## 6. 其他 2025–2026 重要研究（挑三個）

### 6a. Fan et al. 2025「Beware of metacognitive laziness」（BJET）
**書目**：Yizhou Fan（北京大學教育學院）等，*British Journal of Educational Technology*，DOI 10.1111/bjet.13544。收稿 2024-09-30、接受 2024-11-10；抓到的是 early-view PDF（early view：已接受、已上線但尚未排入卷期的版本，卷期欄印 0），ERIC 標示 2025 年 3 月出刊。
- 研究設計：實驗室隨機實驗，四組：ChatGPT 組（AI）、真人專家對談組（HE）、寫作分析工具／檢核表組（CL）、無額外工具組（CN）。任務：讀材料寫一篇文章再修改。量測：內在動機問卷、多通道 SRL（self-regulated learning，自我調節學習：學習者自己規劃、監控、評估學習的歷程）行為序列、文章分數、知識前後測、遷移測驗。
- 樣本：117 名大學生，70% 女性。
- 發現（附數字）：四組事後內在動機無差異（各分量表 p 在 0.33–0.72）。修改前文章分數無差異（F = 1.275，p = 0.286），修改後的「進步幅度」有差異（F = 4.549，p = 0.005，η² = 0.108；η² 是效果量指標，代表組別解釋了多少變異）：AI 組比 CN 組多 1.970 分（p adj = 0.037）、比 HE 組多 2.120（0.025）、比 CL 組多 2.200（0.012）。但知識後測（F = 0.913，p = 0.438）與遷移測驗（F = 0.019，p = 0.996）四組無差異。SRL 歷程頻率與序列有顯著組間差異：AI 組較少自我監控與評估，作者命名為「後設認知怠惰（metacognitive laziness）」：把監控、評估等後設認知責任卸給 AI。
- 限制（作者自述）：樣本小、任務短、女性偏多；單一讀寫任務；無長期追蹤；沒有成熟的「後設認知怠惰」量測工具。
- 來源：https://bera-journals.onlinelibrary.wiley.com/doi/10.1111/bjet.13544（Wiley 403；實際讀的是 PH Bern 存的開放取用 PDF https://ttim.phbern.ch/wp-content/uploads/2025/02/Brit-J-Educational-Tech-2024-Fan-Beware-of-metacognitive-laziness-Effects-of-generative-artificial-intelligence-on.pdf ）【同儕審查】

### 6b. Contractor & Reyes 2026「Experimental Evidence on the Learning Impact of Generative AI」
**書目**：Zara Contractor、Germán Reyes（Middlebury College），IZA Discussion Paper No. 18792，2026 年 7 月；同稿 arXiv:2607.08849（2026-07-09）。
- 研究設計：隨機實驗，兩次面對面監考 session 相隔約一週。第 1 次：學一個陌生技術主題並寫分析短文，隨機分「可用 AI（任何生成式 AI + 傳統資源）」與「禁用 AI」；監考觀察 + ChatGPT 對話紀錄 + 自陳確認合規。第 2 次：所有人無任何資源再考一次、再寫一篇。量測：知識測驗（事實與概念）、開放式短文（人類與 AI 評分）、語言特徵（長度、詞彙多樣性、AI 偵測比例）。
- 樣本：211 名大學生。可用 AI 組 68% 實際使用了 AI；對照組近零。57% 的使用者主要用 AI 解釋概念。
- 發現（附數字）：即時知識測驗，AI 組高 0.27 SD（對照組基線 56.3%）。一週後仍高 5.1 個百分點，約 76% 的即時效果持續。文章品質在有 AI 時變化不大，一週後無 AI 時反而在風格與切題度上進步。作者用對話紀錄把 49% 的使用者歸為「augmentation」（用 AI 解釋概念），其餘為「automation」（用 AI 生成文字）：augmentation 者的測驗增益一週後仍在；automation 者的短期文章品質增益在拿掉 AI 後消失。機制：總學習時間不變，但從打草稿轉向閱讀與搜尋資訊；自陳學習樂趣高 13%。增益在中段學生最大，作者提醒可能擴大差距。
- 限制：工作論文尚未同儕審查；單一學院、單一主題、一週追蹤；「可用 AI」組只有 68% 真的用了（報告的是「意向治療」效果，即按分組而非按實際使用估計，真實使用者的效果會被稀釋）；augmentation／automation 是事後分類，非隨機。與 Bastani（無防呆有害）方向相反，值得對照：任務類型（讀材料寫短文 vs 數學練習題）與量測時點不同。
- 來源：https://docs.iza.org/dp18792.pdf（抓到 PDF 全文）【工作論文】；https://arxiv.org/abs/2607.08849 【預印本】

### 6c. Park, Orozco Vasquez & Conati 2026（EDM 2026）
**書目**："Characterizing Students' LLM Usage Behaviors and Their Association with Learning in Critical Thinking Tasks", arXiv:2605.04534（2026-05-06，修訂 05-29），註明 EDM 2026（教育資料探勘會議）。作者為 UBC（Cristina Conati 實驗室），不是 Stanford／CMU。
- 研究設計：觀察研究（非隨機）。兩學期的研究導向課程，學生要讀、推理、批評學術論文；不限制 LLM 使用，收集作業中的 LLM 使用紀錄，依「學生主動性」分類使用行為，與三次期中考成績做關聯。
- 樣本：兩學期修課學生；摘要頁未給 n（未抓全文）。
- 發現：使用「方式」而非「是否使用」與學習成果相關；主動評估與延伸 AI 輸出者成績較好，被動接受者較差（此為 WebSearch 摘要的說法，全文未讀）。
- 限制：相關性、自選偏差、單一課程；我只讀到摘要頁。
- 來源：https://arxiv.org/abs/2605.04534 【預印本】（細節部分【二手】）

---

## 7. Anthropic 自家教育相關研究

三份皆為 Anthropic 用自家工具 Clio（用 Claude 把大量對話自動分群、摘要成主題，過程中不讓人讀到個別對話的隱私保護分析系統）對 Claude.ai 對話做的匿名化用量分析，公司自行發布、非同儕審查；標【官方報告】但性質是廠商自述。

### 7a. Anthropic Education Report: How University Students Use Claude（2025-04-08）
- 研究設計：Clio 隱私保護分析。從綁高教 email 的 Claude.ai Free／Pro 帳號抓約 100 萬則對話，篩出 574,740 則與學生相關者；單一 18 天資料保留窗口。
- 發現：資工占 38.6%（但美國學位只占 5.4%）；商學 8.9%（學位 18.6%）、健康 5.5%（13.1%）、人文 6.4%（12.5%）偏低。四種互動模式各占 23–29%：直接解題、直接產出、協作解題、協作產出。用途：製作／改善教育內容 39.3%、技術解釋或解答 33.5%。Bloom 分類：Claude 執行的以「創造」39.8%、「分析」30.2% 為主，「記憶」只有 1.8%——作者自己點出「AI 在替學生做高階認知工作」的疑慮。報告也承認有學生要考題答案、改寫以躲抄襲偵測等案例。
- 限制（自述）：早期採用者樣本；只看學生把什麼任務交給 AI，看不到他們之後怎麼用輸出；分類有偽陽性偽陰性；只是整體 AI 使用的部分視角。
- 來源：https://www.anthropic.com/news/anthropic-education-report-how-university-students-use-claude 【官方報告】

### 7b. Education Report: How Educators Use Claude（2025-08-27）
- 研究設計：約 74,000 則高教 email 帳號對話（2025-05-22 至 06-02）+ 22 位 Northeastern 教師的質性研究。
- 發現：課程設計 57%、學術研究 13%、評量學生 7%。「增強」vs「自動化」：課堂教學 77.4% 增強、計畫申請 70.0%、學術諮詢 67.5%；財務／募款 65.0% 自動化、學生成績評估 48.9% 自動化、招生 44.7% 自動化。
- 限制（自述）：篩選只抓到約 1.5% 的高教對話；排除 K-12；早期採用者；單一機構訪談；只有 5–6 月。
- 來源：https://www.anthropic.com/news/anthropic-education-report-how-educators-use-claude 【官方報告】

### 7c. Anthropic Education Report: The AI Fluency Index（2026-02-23）
- 研究設計：9,830 則多輪 Claude.ai 對話（2026-01-20 至 01-26），用 Claude Sonnet 4／Haiku 3.5 分類器判斷 24 項「AI 素養行為」中可觀察的 11 項。對象是一般使用者，非專門針對學生。
- 發現：85.7% 對話有迭代修正；有迭代的對話平均出現 2.67 項素養行為 vs 無迭代 1.33；質疑 AI 推理在迭代對話中出現機率高 5.6 倍；12.3% 對話涉及產出物（程式、文件），這類對話的批判評估行為反而較少（指出缺漏脈絡 −5.2 pp、質疑推理 −3.1 pp、查證 −3.7 pp）。
- 限制（自述）：早期採用者、單週、只看 11 項、二元分類、相關非因果。
- 來源：https://academy.claude.com/tutorials/the-ai-fluency-index（anthropic.com/research/AI-fluency-index 轉址至此）【官方報告】

### 7d. 其他
- Claude for Teachers（2026-07-14）公告沒有自家學習成效數據，只引 Stanford SCALE 的說法「AI 工具對學生的影響好壞參半、取決於實作方式；給老師用的 AI 工具可以強化教學」。https://www.anthropic.com/news/claude-for-teachers 【官方報告】（無數據）

---

## 沒查到或沒確認的事

1. **PNAS 更正（pnas.2518204122）內容**：PNAS 站 403，不知道更正了什麼。Bastani 數字取自 PMC 鏡像的正式版。
2. **SSRN 4895486 頁面**：403。「2024 年以 SSRN 工作論文流傳」的證據是 Knowledge@Wharton 2024-08-27 報導與搜尋結果，不是 SSRN 頁面本身。
3. **Kestin Supplementary Material 1（AI 家教系統提示詞原文）**：nature.com 頁面沒抓到 ESM 連結。「一次只給一步」「不超過幾句話」「鼓勵先自己試」等措辭只有 Hechinger 報導【新聞/部落格】背書。
4. **PISA 2022 各國施測月份的完整清單**：Vol. I 只寫「許多國家在 2022 年 4 月」與三個改期國家（愛爾蘭、荷蘭 10–12 月；柬埔寨 6 月），沒有逐國表。技術報告未查。
5. **PISA 2025 新聞稿（2026-09-08）與 OECD 部落格「PISA findings on AI use…」**：皆 403。新聞稿日期與「每日用 AI 者科學低 28 分」「約 20／30 分」等具體分數差只有新聞轉述，Vol. I 正文找到方向性敘述但未找到這些逐字數字（可能在圖 I.4.13／表 I.B1.4.61 的圖形資料裡，PDF 文字抽取抓不到圖上數值）。
6. **Kosmyna 是否已有期刊版**：搜尋找不到；v2（2025-12-31）仍標 "under review"。PMC12723506「Your brain on ChatGPT」是 BJGP 的評論文章，不是原研究。
7. **Gerlich 2025 "AI Tools in Society"（Societies 15(1):6，n=666，認知卸載）**：MDPI、SSRN、ResearchGate 全部 403，且該文有已發布的更正；未讀到原文，故不列入第 6 節。
8. **"ChatGPT as a cognitive crutch" RCT（ScienceDirect, 2025）**：403 未讀。
9. **Park et al. 2026（6c）全文**：只讀到 arXiv 摘要頁，n 與效應大小未確認。
10. **Stanford／CMU 2025–2026 的 LLM 學習 RCT**：搜尋只找到 Stanford Report 與 CMU 新聞的評論性文章與 Stanford SCALE 證據整理頁，沒有找到符合「Stanford 或 CMU 主導的 RCT」的一手論文；未逐一深入。
11. **PISA 2022 Vol. II 中 "Goldilocks" 段落引用的原始文獻（Przybylski & Weinstein 2017）**未查。
