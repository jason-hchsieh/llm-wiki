# 與 AI 共學:證據、機制與實作選項

日期:2026-09-19。起點是一場一小時的 ChatGPT 語音對話(`~/Downloads/ChatGPT-問候與回應-20260919-1131.md`),對話結尾歸納出四個 take-away。這份筆記查證那些說法,並整理成能落地為 Claude Code skill 或 agent 的設計依據。

證據等級標籤全篇統一:【同儕審查】【預印本】【工作論文】【官方報告】【開源專案】【新聞/部落格】【二手】【未證實】。標籤後面逗號接的是補充說明,例如【同儕審查,僅摘要】表示來源是同儕審查論文但只讀到摘要。
「二手」指沒讀到擁有該主張的原始文件,只讀到轉述。「未證實」指連轉述都找不到可靠出處。

原始查核紀錄(含每篇論文逐句抄下的數字與行號)在 `docs/research/sources/` 底下四份檔案:`2026-09-19-A-learning-science.md`、`2026-09-19-B-ai-learning.md`、`2026-09-19-C-claude-code-mechanics.md`、`2026-09-19-D-prior-art.md`。本文只留結論。

---

## 摘要

1. 對話裡的核心主張「留住困難、先猜再看、自己解釋、AI 只做執行」每一條都有同儕審查的一手證據,效果量落在中到大(d 0.5 到 1.5)。效果量是用標準差當單位描述「差距有多大」的數字,d 或 g 讀作 0.2 小、0.5 中、0.8 大。
2. 但「一週一次」「先想 30 秒到 1 分鐘」「無 AI 日」這些具體節奏是 ChatGPT 自己編的,沒有研究直接支持。
3. PISA 2022 的跌幅確實是歷年最大,但施測在 2022 年 4 月,ChatGPT 是 2022 年 11 月才上線,所以「AI 造成那次跌幅」在時間上不可能。ChatGPT 當時搜尋後沒有指出這一點。
4. 剛公布的 PISA 2025(2026-09)顯示閱讀再掉 14 分、數學再掉 9 分。OECD 自己說 AI 使用與成績的關係「不必然代表 AI 有負面影響」。
5. 兩個關於 AI 家教的隨機對照試驗結論一致:沒有防呆的 AI 讓閉卷成績掉 17%,加了「只給提示」的 AI 能抵銷傷害,設計得好的 AI 家教能贏過課堂主動學習 0.63 個標準差。
6. Claude Code 文件確認:子代理(subagent)拿不到向使用者提問的工具,所以所有「要使用者回答」的步驟必須留在主對話。這一點直接決定了 skill 比 agent 合適。
7. 強制力只存在於 hook。寫在 CLAUDE.md 或 skill 裡的規則,文件明說「是 context,不是強制設定」。
8. 既有專案裡沒有任何一個真正做「先寫下預測、鎖住、才揭曉」的設計。這是空白,也是這個專案的差異點。
9. 有效果證據的「不給答案」設計都允許最後揭曉、允許確認答案,只是節流。開源 skill 常見的「絕不給答案、即使懇求」沒有證據支持。
10. 三個最重要的設計決策:預測閘門用 hook 強制還是 prompt 提醒;互動步驟放 skill、盲測步驟放 subagent;揭曉時機採「預測鎖定後立即揭曉」而非「永不揭曉」。

---

## A. 學習科學的證據

### A1. 合意困難(desirable difficulties)

合意困難是指:學習當下讓你表現變差、感覺更吃力,但長期記憶與遷移反而更好的操作。

- **一手來源**:Bjork (1994) 章節,《Metacognition: Knowing about Knowing》pp. 185–205,全文 https://gwern.net/doc/psychology/spaced-repetition/1994-bjork.pdf 【同儕審查,學術專書章節】;Bjork & Bjork (2011) 章節,作者實驗室 PDF https://bjorklab.psych.ucla.edu/wp-content/uploads/sites/13/2016/07/EBjork_RBjork_2011.pdf 【同儕審查,學術專書章節】。
- **關鍵結論**:1994 年章節列出五類操作:變動練習情境、交錯練習、分散練習、降低回饋頻率、把測驗當學習事件。章節引的西班牙字彙實驗:訓練間隔 0 天、1 天、30 天,30 天後回憶率分別是 33%、64%、72%,但訓練當下的表現剛好相反。2011 年章節引的交錯練習實驗:一週後新題正確率,交錯組 63% 對集中組 20%。
- **重要但書**(2011 原文):學習者若沒有足夠背景知識去應付,困難就從「合意」變成「不合意」。
- **設計含意**:AI 最自然的失敗模式是「把事情變簡單」。系統要刻意保留一部分困難,並且要告訴使用者「現在變難是刻意的」,否則使用者會用當下的流暢感評價系統。難度需要調節器,不能一律加難。

### A2. 提取練習 / 測驗效應(retrieval practice / testing effect)

測驗效應是指:把「考自己」當學習手段,比同樣時間重讀更能提升長期記憶。

- **一手來源**:Roediger & Karpicke (2006), *Psychological Science* 17(3), 249–255。全文 https://colinallen.dnsalias.org/Readings/2006_Roediger_Karpicke_PsychSci.pdf ;PubMed https://pubmed.ncbi.nlm.nih.gov/16507066/ 【同儕審查】。
- **關鍵數字**(抄自論文 Results):實驗 1,一週後回憶率,測驗組 56% 對重讀組 42%,d = 0.83。5 分鐘後則相反:重讀 81% 對測驗 75%。實驗 2,讀一次考三次(STTT)一週後 61%,讀四次(SSSS)只有 40%,d = 1.26。讀四次那組對「一週後記得住」的信心反而最高。
- **設計含意**:預設互動不該是「AI 講解,使用者點頭」,而是「AI 先問,使用者答,才給解答」。效益只在延遲測驗看得到,所以評估自己有沒有學到,要看隔天或隔週,不能看當場。使用者的「我覺得懂了」和實際記憶是反向的。

### A3. 生成效應(generation effect)

生成效應是指:自己產出答案(哪怕只是補一個字)比直接讀現成答案記得更牢。

- **一手來源**:Slamecka & Graf (1978), *JEP: Human Learning and Memory* 4(6), 592–604。全文掃描 https://andymatuschak.org/prompts/Slamecka1978.pdf 【同儕審查】。
- **關鍵數字**:五個實驗一致。實驗 5 線索回憶,生成組 .68 對閱讀組 .61。實驗 4 顯示優勢只出現在「被生成的那個詞」,不在旁邊的刺激詞。論文未報告 d 值(1978 年慣例)。
- **設計含意**:讓使用者填的空,必須正好是你希望他記住的那一步,不是周邊敘述。

### A4. 先猜再看 / 前測效應(pretesting effect)

前測效應是指:讀到答案之前先被問一次,即使答錯,之後學同樣內容也學得更好。

- **一手來源**:Richland, Kornell & Kao (2009), *JEP: Applied* 15(3), 243–257,全文 https://learninglab.uchicago.edu/Pre-Testing_files/RichlandKornellKao.pdf 【同儕審查】;Kornell, Hays & Bjork (2009), *JEP: LMC* 35(4), 989–998,全文 https://web.williams.edu/Psychology/Faculty/Kornell/Publications/Kornell.Hays.Bjork.2009.pdf 【同儕審查】。
- **關鍵數字**:Richland 實驗 1,前測組最終測驗 75% 對延長閱讀組 56%,d = 1.1,而且前測時受試者答錯率高達 95%。延遲一週的實驗 4 仍是 55% 對 45%,d = 0.45。Kornell 實驗 3,d = 1.49;延遲 38 小時的實驗 5,d = 0.94。Kornell 另一個發現:猜錯與完全空白的後續學習效果沒有顯著差異(.44 對 .51,p = .41)。
- **設計含意**:AI 解釋新概念之前先丟一個使用者幾乎答不出的問題,是有實驗支持的預設動作。使用者按「我想不出來」也保留大部分效益,所以不必逼他一定打出答案。但這些實驗裡猜完一律**立即**顯示正解,揭曉不能拖。

### A5. 高信心錯誤的超額修正(hypercorrection)

超額修正是指:答錯時如果本來很有把握,看到正解後反而更容易改對。

- **一手來源**:Butterfield & Metcalfe (2001), *JEP: LMC* 27(6), 1491–1494。只取得官方摘要 https://pubmed.ncbi.nlm.nih.gov/11713883/ 【同儕審查,僅摘要】。同作者 2006 年續作全文 http://www.columbia.edu/cu/psychology/metcalfe/PDFs/Butterfield_Metcalfe_2006.pdf 【同儕審查】。
- **關鍵結論**:2001 摘要原文「highly confident errors were the most likely to be corrected in a subsequent retest」。摘要沒有數字;網路流傳的 gamma = .36 無法在該篇確認【未證實】。2006 年那篇確認效應不能只用「題目本來就簡單」解釋(偏相關 pr = .11,p < .001)。
- **設計含意**:作答時一併收信心(高/中/低三選一即可)。「高信心且錯」的那一題最值得花版面,不是最該迴避的。

### A6. 自我解釋(self-explanation)

自我解釋是指:一邊讀一邊對自己說明「為什麼是這樣」,而不是複誦字面。

- **一手來源**:Chi et al. (1989), *Cognitive Science* 13(2), DOI 10.1207/s15516709cog1302_1, https://onlinelibrary.wiley.com/doi/abs/10.1207/s15516709cog1302_1 【同儕審查,正文未取得,數字為作者 1994 年自述,屬二手】;Chi, de Leeuw, Chiu & LaVancher (1994), *Cognitive Science* 18(3), 439–477,全文 https://andymatuschak.org/files/papers/Chi%20et%20al%20-%201994%20-%20Eliciting%20self-explanations%20improves%20understanding.pdf 【同儕審查】;Bisra et al. (2018) 後設分析,*Educational Psychology Review* 30(3), 703–725,全文 https://gwern.net/doc/psychology/spaced-repetition/2018-bisra.pdf 【同儕審查】。
- **關鍵數字**:1989 是 8 人的觀察研究,不是實驗,不能單獨支持因果。1994 隨機分派:每讀一句就提示解釋,前後測增幅 32% 對重讀組 22%;在需要推論的難題上 22.6% 對 12.5%。Bisra 2018 合併 69 個效果量、5,917 人,整體 g = .55(95% CI .45 到 .65)。三個調節結果對設計特別重要:對照組若也拿到現成講解,g 掉到 .35;用選擇題當提示 g = .24 且不顯著;問「你覺得自己懂了嗎」這種後設認知提示 g = .19 且不顯著。
- **設計含意**:「先提示解釋、後給講解」的順序有數據支撐。提示要開放式,要引導說明機制,不要問「懂了嗎」。

### A7. 費曼技巧(Feynman technique)

- **一手來源:沒有。** 找不到任何 Feynman 本人提出四步驟方法的著作或講稿。科普網站彼此轉引「最早出現於 2011 年」,也無可查證出處。歸屬於 Feynman 這件事標【未證實】;作為流行學習法的描述標【二手】。
- **最接近且有證據的構念**:拆開來看,「用簡單語言重講」對應自我解釋(A6,g = .55);「教一個不懂的人」對應教學預期與實際教學。Nestojko et al. (2014), *Memory & Cognition* 42(7), https://link.springer.com/article/10.3758/s13421-014-0416-z ,只取得摘要:預期要教的人自由回憶更完整、主要觀點答得更好,無數字【同儕審查,僅摘要】。Fiorella & Mayer (2013), *Contemporary Educational Psychology* 38(4), https://www.sciencedirect.com/science/article/abs/pii/S0361476X13000209 付費牆,轉述自作者本人 2023 年回顧章節 https://www.unh.edu/teaching-learning-resource-hub/sites/default/files/media/2023-06/itow-learning-by-teaching-fiorella.pdf 【二手】:「預期要教」只在立即測驗有好處,「真的教了」才在延遲測驗有好處。
- **設計含意**:光說「等下要教別人」不夠,要有一個實際產出解釋的環節。在他產出之前不要先餵標準講解。

### A8. 分散練習(spacing)

- **一手來源**:Cepeda et al. (2006) 後設分析,*Psychological Bulletin* 132(3), 354–380,全文 https://augmentingcognition.com/assets/Cepeda2006.pdf 【同儕審查】;Dunlosky et al. (2013) 總覽,*Psychological Science in the Public Interest* 14(1), 4–58,全文 https://www.whz.de/fileadmin/lehre/hochschuldidaktik/docs/dunloskiimprovingstudentlearning.pdf 【同儕審查】。
- **關鍵結論**:Cepeda 收錄 271 組分散對集中的比較,只有 12 組無效或負向。最佳學習間隔隨保留目標拉長而拉長:目標是六個月以上,第一次複習至少隔一個月。這篇沒有給統整的 d 值;流行的「最佳間隔是保留期的 10 到 20%」不在這篇,應為 Cepeda et al. (2008) *Psychological Science* 19(11),本次未核對【未證實】。Dunlosky 把練習測驗與分散練習評為「高效用」,自我解釋與交錯評為「中效用」(理由是缺教室情境驗證,不是效果小),摘要、畫重點、重讀評為「低效用」。
- **設計含意**:「一週一次」不是從證據來的數字,只是一個可行的分散節奏。真正該做的是把上次紀錄留下來,下次開場先抽問。另外「AI 幫你整理成漂亮筆記」正是被評為低效用的那一組活動。

### A9. 能力錯覺(illusion of competence)

- **一手來源**:Koriat & Bjork (2005), *JEP: LMC* 31(2), 187–194,全文 https://bjorklab.psych.ucla.edu/wp-content/uploads/sites/13/2016/07/Koriat_RBjork_2005.pdf 【同儕審查】。
- **關鍵數字**:當答案還在畫面上時做「我記得住嗎」的判斷,反向詞對的自評 75.7 對實際回憶 60.3,高估約 15 個百分點。錯覺來自「判斷當下答案還看得到」。
- **設計含意**:絕不要在 AI 的解釋還在螢幕上時問「你懂了嗎」。要收自評就延遲,而且只給線索不給答案。

---

## B. AI 與學習的研究

### B1. Bastani et al.:沒有防呆的生成式 AI 會傷害學習

- **正式版本**:*PNAS* 122(26), 2025-06-25, DOI 10.1073/pnas.2422633122。全文鏡像 https://pmc.ncbi.nlm.nih.gov/articles/PMC12232635/ 【同儕審查】。前身是 2024 年 SSRN 工作論文 4895486, https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4895486 【工作論文,頁面未抓到】;Wharton 2024-08-27 報導 https://knowledge.wharton.upenn.edu/article/without-guardrails-generative-ai-can-harm-education/ 【新聞/部落格】。
- **研究設計**:隨機對照試驗(RCT,把受試者隨機分組,只有部分組接受介入)。土耳其一所高中,以班級為單位分三組:GPT Base(原生 GPT-4)、GPT Tutor(加老師寫的提示詞,只給提示不給答案)、對照組(課本)。每節課:講課、輔助練習、閉卷考。
- **樣本**:約 1,000 名 9 到 11 年級學生,2023 年秋。
- **發現**:練習階段 GPT Base 比對照組進步 48%,GPT Tutor 進步 127%。閉卷考:GPT Base 比對照組**差 17%**;GPT Tutor 與對照組無顯著差異。機制:GPT Base 組大量直接要答案、複製解法,而且自己沒察覺學習受損。
- **限制**:單一學校、單一科目、只量同節課的考試,無長期追蹤。
- **對本專案的意義**:「只給提示」能把傷害抵掉,但沒有帶來額外增益。要有增益,設計得再更多(見 B2)。

### B2. Kestin et al.:AI 家教贏過課堂主動學習

- **來源**:*Scientific Reports* 15, 17458, 2025-06-03, https://www.nature.com/articles/s41598-025-97652-6 (無頭抓取會被導去登入頁,免登入全文用 https://pmc.ncbi.nlm.nih.gov/articles/PMC12179260/ )【同儕審查】。補充資料含完整 system prompt:https://static-content.springer.com/esm/art%3A10.1038%2Fs41598-025-97652-6/MediaObjects/41598_2025_97652_MOESM1_ESM.docx
- **研究設計**:交叉設計 RCT(每個學生兩種條件都經歷,順序隨機)。哈佛普通物理課,兩堂課分別用 AI 家教在家學與課堂主動學習。
- **樣本**:符合條件者 194 人。
- **發現**:效果量 0.63 個標準差(線性迴歸估計;受天花板效應壓低,天花板效應是指分數擠在滿分附近、差距被低估;改用分位數迴歸估 0.73 到 1.3,分位數迴歸是對分布各段而非平均值做迴歸,較不受天花板影響)。AI 組中位學習時間 49 分鐘,比課堂 60 分鐘短。投入感與動機顯著較高。
- **防呆設計**(補充資料原文):回覆簡短、一次只給一步、可以確認答案對錯、學生第一則訊息就要答案時先請他試試看、把老師寫好的逐步解答塞進提示詞讓 AI 有標準答案可對。作者發現光靠提示詞無法讓 GPT-4 按順序處理多步驟題,改用平台介面強制一步一步帶。
- **限制**:只量兩堂課的即時學習,無長期保留;每題都要人工準備標準解答;學生是第一次接觸主題;作者明說不假設在需要跨概念綜合的高階任務也會贏。

### B3. OECD PISA 2022 與 PISA 2025

PISA 是 OECD 每三年一次對 15 歲學生的數學、閱讀、科學國際評量。

- **PISA 2022 施測時間**:Vol. I 原文「If the PISA assessment was conducted in April 2022, as was the case in many countries」。只有愛爾蘭、荷蘭移到 2022 年 10 到 12 月,柬埔寨 6 月。ChatGPT 上線是 2022-11-30。**所以 2018 到 2022 的跌幅不可能是 AI 造成的。** 來源 https://www.oecd.org/content/dam/oecd/en/publications/reports/2023/12/pisa-2022-results-volume-i_76772a36/53f23881-en.pdf 【官方報告】。
- **「歷年最大跌幅」準確**:OECD 平均數學掉近 15 分、閱讀掉約 10 分;此前兩次評量之間數學從未掉超過 4 分、閱讀 5 分,OECD 用「unprecedented」形容。
- **OECD 自己的歸因**:「the decline can only partially be attributed to the COVID-19 pandemic」。閱讀從 2012、科學從 2009 就開始下滑。停課長短與跌幅之間看不出明顯關係。
- **螢幕裝置與成績**(Vol. II 與 PISA in Focus):在校每天用裝置學習最多 1 小時的學生,控制社經後比完全不用者高 14 分;超過 1 小時轉負。上數學課時「被別人的裝置分心」的學生低 15 分。OECD 稱之為 Goldilocks 假說(適量不害,過量才害)。全部是橫斷面相關,也就是同一時間點量到的「一起出現」,不能說誰造成誰。來源 https://www.oecd.org/content/dam/oecd/en/publications/reports/2023/12/pisa-2022-results-volume-ii_222a5ef6/a97db61c-en.pdf 【官方報告】。
- **PISA 2025 已於 2026 年 9 月公布**:Vol. I https://www.oecd.org/content/dam/oecd/en/publications/reports/2026/09/pisa-2025-results-volume-i_5265bfb1/73451bc5-en.pdf 【官方報告】。2022 到 2025 閱讀再掉約 14 分、數學掉 9 分,三科皆為歷史最低。首次加入 AI 使用題:針對摘要文本、初步研究、草擬作業這類任務,不用 AI 者在多數國家勝過使用者;適度使用者(每月一次到每週兩次)表現最好。若學校有教「評估 AI 生成資訊的品質」,頻繁用 AI 學習的學生略高於不用者。OECD 原文警告:「These relationships do not necessarily imply a negative impact of AI use on science performance, but may reflect a complex mix of who adopts AI and how they use it.」秘書長前言:AI 「should add to students' effortful learning rather than replace it」。網傳「每日用 AI 者低 28 分」等數字只在新聞找到,例如 https://technode.global/2026/09/08/students-avoiding-ai-for-schoolwork-outscore-peers-in-science-bar-one-use-oecd/ 【二手】。

### B4. MIT Media Lab「Your Brain on ChatGPT」(Kosmyna et al.)

- **來源**:arXiv:2506.08872,v2 2025-12-31,頁腳仍印「Preprint, under review」【預印本】。https://arxiv.org/abs/2506.08872
- **研究設計**:三組各 18 人(只用 ChatGPT、只用 Google、不用工具)寫 SAT 作文三次,第四次對調,全程戴 32 通道腦波帽(EEG,量頭皮電活動)。
- **樣本**:54 人,波士頓五所名校,第四次只有 18 人回來。
- **發現**:腦區連結強度不用工具組最強、ChatGPT 組最弱。第一次寫完後 ChatGPT 組 83.3% 無法引述自己剛寫的句子,另兩組各 11.1%。作者提出「認知負債」一詞。
- **限制**:作者自述人數少、單一地區、只用 ChatGPT、無長期追蹤。另有外部評論 https://arxiv.org/abs/2601.00856 【預印本,只讀摘要】質疑樣本數、可重現性與 EEG 分析方法。**這篇的媒體聲量遠大於證據強度。**

### B5. Microsoft Research Lee et al.:生成式 AI 與批判思考

- **來源**:CHI '25, DOI 10.1145/3706598.3713778。PDF https://www.microsoft.com/en-us/research/wp-content/uploads/2025/01/lee_2025_ai_critical_thinking_survey.pdf 【同儕審查】。
- **研究設計**:線上問卷,非實驗。每人描述三個真實 AI 使用案例並自評批判思考程度。
- **樣本**:319 名每週用 AI 的知識工作者,936 個案例。
- **發現**:對「AI 能做好這件事」的信心越高,自陳批判思考越少(β = −0.69,p < .001;β 是迴歸係數,表示自變項每多一單位,結果變多少);對自己能力的信心越高,批判思考越多(β = 0.26)。批判思考的性質從「執行」轉向「驗證資訊、整合、監督」。
- **限制**:全部自陳,相關非因果,受訪者常把「省力」和「少思考」混為一談。

### B6. 其他 2025 到 2026 年研究

- **Fan et al. 2025「Beware of metacognitive laziness」**,*BJET*, DOI 10.1111/bjet.13544, https://bera-journals.onlinelibrary.wiley.com/doi/10.1111/bjet.13544 (Wiley 對抓取程式回 403,實際讀的是開放取用鏡像 https://ttim.phbern.ch/wp-content/uploads/2025/02/Brit-J-Educational-Tech-2024-Fan-Beware-of-metacognitive-laziness-Effects-of-generative-artificial-intelligence-on.pdf )【同儕審查】。117 名大學生四組隨機(ChatGPT、真人專家、檢核表、無工具)讀材料寫文章。ChatGPT 組文章修改幅度最大(η² = .108,η² 是組別解釋了多少變異的比例),但知識後測與遷移測驗四組無差異。ChatGPT 組較少自我監控與評估,作者稱「後設認知怠惰」。
- **Contractor & Reyes 2026**,IZA DP 18792 https://docs.iza.org/dp18792.pdf 【工作論文】。211 名大學生,可用 AI 組即時測驗高 0.27 SD,一週後 76% 的效果仍在。用對話紀錄分類:用 AI 解釋概念的人(augmentation)一週後增益仍在;用 AI 生成文字的人(automation)增益消失。方向與 Bastani 相反,差別在任務類型與有無防呆。
- **Park et al. 2026**,arXiv:2605.04534, https://arxiv.org/abs/2605.04534 【預印本,只讀摘要】。觀察研究:主動評估與延伸 AI 輸出者成績較好,被動接受者較差。
- **Anthropic 教育報告**(廠商自述,非同儕審查【官方報告】):2025-04 學生報告顯示 Claude 執行的任務以「創造」39.8%、「分析」30.2% 為主,「記憶」只 1.8%,Anthropic 自己點出「AI 在替學生做高階認知工作」的疑慮。https://www.anthropic.com/news/anthropic-education-report-how-university-students-use-claude

### B7. 這一節的綜合判斷

證據一致指向三件事。第一,傷害來自「AI 替你做了本該由你做的認知工作」,不是來自 AI 本身(Bastani 對照 GPT Tutor;Contractor 的 augmentation 對 automation;Fan 的後設認知怠惰)。第二,只要防呆到位,AI 家教能贏過課堂(Kestin)。第三,所有大規模跌幅的說法(PISA)在時間或因果上都不能歸給 AI,OECD 自己也這麼說。

---

## C. Claude Code 的實作機制

文件根目錄 https://docs.anthropic.com/en/docs/claude-code/ 會 301 轉址到 https://code.claude.com/docs/en/ ,以下 URL 用轉址後的。查核時發現 WebFetch 的模型摘要至少錯了三處(hooks 的 JSON 巢狀層級、事件清單漏 SessionEnd、subagent 欄位細節),所以 C 節所有 schema 都以 curl 下來的原始 markdown 為準。

### C1. Skill 與 subagent 的差別

Skill 是一個資料夾裡的 `SKILL.md`,寫「某件事怎麼做」的指示。Frontmatter 是這個檔案開頭用 `---` 夾住的設定區,放名稱、觸發條件、工具限制等欄位。Subagent 是獨立開的 Claude 實例,有自己的 context、system prompt 與工具集。

| 面向 | Skill | Subagent(非 fork) | 文件根據 |
|---|---|---|---|
| 誰載入 context | 內容進**主對話**,而且「stays there across later turns」 | 全新 context,看不到對話歷史、看不到已讀的檔案 | skills.md 行 514;sub-agents.md 行 1035 |
| 能不能保有對話 | 能,就是主對話 | 只回摘要;Claude 可用 SendMessage 續談,但那是 Claude 對 subagent | sub-agents.md 行 9、940、1080 |
| 能不能向使用者提問 | 能,主對話有 `AskUserQuestion` | **不能。** `AskUserQuestion` 被第一層過濾器無條件移除,「even when listed in the `tools` field」 | sub-agents.md 行 409 到 418 |
| 能不能自動觸發 | 能,靠 `description`;`disable-model-invocation: true` 可關掉 | 能,靠 `description` 委派 | skills.md 行 336 到 352 |
| 能不能限制工具 | `allowed-tools` / `disallowed-tools`,但**只撐一個回合**,「clears when you send your next message」 | `tools` 白名單 / `disallowedTools` 黑名單,整個 subagent 生命期有效 | skills.md 行 335 到 336;sub-agents.md 行 299 |
| 獨立 context 選項 | `context: fork` 會丟到 subagent 跑,「starts without your conversation history」 | 本來就是 | sub-agents.md 行 591 |

來源:https://code.claude.com/docs/en/skills.md 、https://code.claude.com/docs/en/sub-agents.md 【官方文件】。

一個術語陷阱:skill 的 `context: fork` 產生的是**不繼承**對話的 subagent;`/subtask` 指令的 fork 則是**繼承整段對話**。兩者都叫 fork,行為相反。

「只給提示不給答案」怎麼做:純 prompt 可以寫,但文件明說 CLAUDE.md 與 skill 文字「Claude treats them as context, not enforced configuration」(memory.md)。想要硬約束有兩條路。一是 subagent 的 `tools` 白名單把 Edit/Write 拿掉,讓「不幫你寫」變成物理事實。二是 Khanmigo 的做法(見 D4):限制 AI 能看、能算的範圍,讓它沒有材料可以洩答。

Slash command 已與 skill 合併:「Custom commands have been merged into skills」,`.claude/commands/deploy.md` 與 `.claude/skills/deploy/SKILL.md` 等價(skills.md 行 16)。

### C2. Hook 能不能做「回答前先要求使用者寫下預測」的閘門

Hook 是在 Claude Code 生命週期某個時點自動執行的東西,可以是 shell 指令、也可以交給模型判斷。來源 https://code.claude.com/docs/en/hooks.md 與 https://code.claude.com/docs/en/hooks-guide.md 【官方文件】。

**能做到的:**

1. `UserPromptSubmit` hook 收得到使用者打的字(`prompt` 欄位),可以檢查預測檔是否存在、非空、符合格式。不合格就回 `decision: "block"` 加 `reason`。
2. `UserPromptExpansion` hook 能擋住使用者直接打的 `/指令`(文件範例:「block `/deploy` unless an approval file is present」)。`PreToolUse` 擋不到直接打的指令。
3. `Stop` hook 回 `decision: "block"` 可以不讓 Claude 結束回合,例如「沒寫紀錄檔不准收工」。連續 8 次上限。
4. hook 不只有 shell。`type: "prompt"` 交給 Haiku 判斷,`type: "agent"` 開一個能讀檔的子代理判斷(60 秒、50 turns)。所以「判斷預測像不像預測」在文件上是做得到的。

**做不到或代價高的,要寫進設計文件:**

- Command hook 只能做確定性檢查。使用者寫「預測:我不知道」就能通過。
- `UserPromptSubmit` 的 block 會**抹掉 prompt**,使用者要重打;`reason` 只顯示給使用者,**不進 context**,Claude 不知道發生過。
- 預設 30 秒逾時,而且逾時是 fail-open:prompt 照樣送達。
- hook「can't trigger `/` commands or tool calls」,所以 hook 不能主動彈出提問框。被 Stop hook 擋下後,要靠 Claude 自己呼叫 `AskUserQuestion` 把發言權交回使用者,`reason` 文字要明白寫這件事。
- prompt/agent 型 hook 的判斷者本身也是 LLM,可被說服、會誤判。

結論:hook 適合當**輕量、確定性的門檻**(檔案存在、格式、擋直接指令),不適合當「這個預測夠不夠好」的裁判。裁判留給主對話裡的 Claude 和使用者自己。

### C3. 跨 session 的學習紀錄怎麼存

來源 https://code.claude.com/docs/en/memory.md 【官方文件】。

| 機制 | 位置 | 特性 | 適合放什麼 |
|---|---|---|---|
| CLAUDE.md(專案層) | `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 每次啟動載入;建議 200 行以內 | 學習流程的常駐規則 |
| CLAUDE.local.md | `./CLAUDE.local.md` | 個人、不進版控 | 個人偏好(想被推多硬) |
| `@` import | CLAUDE.md 內寫 `@path` | 啟動時展開,最多 4 跳 | 把「當前學習主題」檔案拉進 context |
| Auto memory | `~/.claude/projects/<project>/memory/` | 預設開啟;非 git 專案以專案根目錄為鍵;機器本機 | Claude 自己觀察到的習慣 |
| 專案檔案 | 自訂,如 `learning-records/0001-<slug>.md` | 只有被讀時才進 context | 每次的已知/假設/預測/結果/解釋 |

關鍵句(memory.md 開頭):CLAUDE.md 與 auto memory「Claude treats them as context, not enforced configuration. To block an action regardless of what Claude decides, use a PreToolUse hook instead.」

本機 `~/.claude/skills/teach/SKILL.md` 的做法可以直接沿用(本機檔案,已讀,無外部來源):`disable-model-invocation: true`、一個 `learning-records/0001-<dash-case-name>.md` 遞增編號目錄、加一份 `NOTES.md` 放偏好。`learning-companion` skill(本機 `~/.claude/skills/synced/e3ea6783-178f-4f3c-8065-51946297de81_5ac00394-57b7-4139-8c5e-7fd7ec120508/learning-companion/SKILL.md`,已讀)因為針對純聊天環境,把記憶做成「貼回 Markdown 區塊」;在 Claude Code 裡直接改成檔案即可。

### C4. Output style 與 `TODO(human)`

來源 https://code.claude.com/docs/en/output-styles.md 【官方文件】。內建 Learning 風格原文:「Claude will not only share "Insights" while coding, but also ask you to contribute small, strategic pieces of code yourself. Claude Code will add `TODO(human)` markers in your code for you to implement.」這是 Anthropic 官方「把思考留給人」的先例。文件對機制只有這一句,沒有說偵測、等待、驗證怎麼做【未證實】。

兩個限制:output style「apply to the main conversation and to a fork」,「Other subagents run their own system prompt, so styles don't change how they respond」。也就是 Learning 風格不會傳給 subagent。另外它只覆蓋「寫程式」情境,判準全是軟體工程語彙。

### C5. 流程步驟對機制的對照表

| 步驟 | 最適合的機制 | 理由 |
|---|---|---|
| 1. 寫下已知與問題 | **Skill**(`disable-model-invocation: true`,使用者手動 `/` 開啟)+ 寫入固定路徑的紀錄檔 | 需要來回對話,只有主對話能問使用者。skill 內容常駐整段對話。檔案是後面 hook 唯一能確定性檢查的東西。 |
| 2. 提假設與預測 | **主對話 + `UserPromptSubmit` command hook** 檢查預測檔存在且含「預測」「信心」欄位;`UserPromptExpansion` 擋 `/answer` 之類的直接揭曉指令 | hook 做輕量門檻(C2)。同時收信心,為超額修正(A5)鋪路。 |
| 3. AI 建 testcase 跑實驗 | **Subagent**(`tools: Read, Grep, Glob, Bash, Write`,禁止 Edit 既有原始碼) | 「Each subagent starts with a fresh, isolated context window」,出題者天然看不到使用者的預測,是盲測。長輸出留在 subagent context 不污染主對話。 |
| 4. 比對結果 | **主對話**讀 subagent 回傳的摘要;可加 `PostToolUse` 的 `updatedToolOutput` 把工具輸出改寫成「只給證據、不給結論」 | 比對是使用者要做的認知工作,AI 只擺出證據。Kornell 2009 要求猜完立即揭曉,所以這一步不能拖。 |
| 5. 使用者自述解釋 | **主對話**,`AskUserQuestion` 或直接等使用者打字;`Stop` hook 在解釋檔為空時 block | 唯一能向使用者提問的地方。Bisra 2018:解釋之前不要先給講解。 |
| 6. AI 出遷移題 | **Skill 設 `context: fork`**(出題規則)或另一個 subagent | 「start without your conversation history」,出題者沒看過使用者的解釋,不會照著出。 |
| 7. 記錄到檔案 | **`Stop` hook(command)** 每回合檢查紀錄檔已更新;紀錄放 `learning-records/NNNN-<slug>.md`;長期規則放 `CLAUDE.local.md` | 「Stop hooks fire whenever Claude finishes responding」,是最可靠的落檔點。auto memory 不跨機器。 |

三個貫穿全表的結構性限制:強制力只在 hook;output style 不傳給 subagent;只有主對話與 fork 能跟使用者互動。

---

## D. 已有的類似專案或做法

### D1. Claude Code 內建 Learning output style 與官方 `learning-output-style` plugin 【官方文件】【開源專案】

- URL:https://code.claude.com/docs/en/output-styles ;plugin https://github.com/anthropics/claude-code/tree/main/plugins/learning-output-style
- **做法**:plugin 不是 output style 檔,而是 `SessionStart` hook 把教學指令注入 context。注入內容有兩張清單。該請使用者寫的:「Business logic with multiple valid approaches / Error handling strategies / Algorithm implementation choices / Data structure decisions」。不該請使用者寫的:「Boilerplate or repetitive code / Obvious implementations / Configuration or setup code」。交棒前先搭好鷹架(建檔、簽名、註解、標 TODO),每次 5 到 10 行。Insight 只進對話不進程式碼。
- **可借鏡**:「哪些該讓人做」寫成兩張具體清單而不是抽象原則;裝了就生效,不靠使用者記得切換。
- **不適用**:只有「你來寫」,沒有「你先猜」。使用者說「你幫我填」就破功。plugin 自述與內建版不同,內建版 prompt 未公開。

### D2. Kestin et al. 2025 的 PS2 Pal system prompt 【同儕審查】

唯一有 RCT 支撐又公開全文的設計。論文 https://www.nature.com/articles/s41598-025-97652-6 ,補充資料 https://static-content.springer.com/esm/art%3A10.1038%2Fs41598-025-97652-6/MediaObjects/41598_2025_97652_MOESM1_ESM.docx 。原文七條約束,摘要:回覆簡短;一次只給一步,不在單一訊息給完整解答;可以確認答案對錯;學生第一則訊息就要答案時,鼓勵先試試看;假設學生第一次學。另外把老師寫的逐步解答塞進 prompt,讓「知道答案」與「說出答案」分離。

- **可借鏡**:「一次一步」比「絕不給答案」務實,它靠節流不靠封鎖。「先試試看」的觸發條件寫得精準,只擋第一則就要答案的情況。
- **不適用**:每題要人工準備標準解答,不能擴展到沒有預先解答的開放任務,例如真實 codebase 的探索。

### D3. Khan Academy Khanmigo 【官方文件】【新聞/部落格】

- URL:https://blog.khanacademy.org/how-khan-academy-is-building-a-better-ai-tutor-our-most-recent-learnings/
- **做法**:唯一公開的具體機制是「把數學 agent 限制成只檢查學生已經寫出的步驟,不讓它把剩下的步驟算完」,原文量測到洩答率降 50%。他們把「在學生提交前就給出答案」當護欄指標逐次量測。
- **可借鏡**:**限制 AI 的工作範圍勝過叮嚀它不要說。** 對應到 agent 設計就是限制工具、限制能讀的檔案、限制推理範圍。
- **不適用**:system prompt 未公開;情境是有標準答案的 K-12(幼稚園到高中)題庫。

### D4. GitHub 開源 socratic skill 【開源專案】

- `bevibing/socrates-skill`(316 星)https://github.com/bevibing/socrates-skill :硬規則「NEVER give a direct answer... even if the user begs」。最可抄的是 Anti-Patterns 清單,例如「Stating the answer then asking "do you understand?"」「Giving hints so obvious they are effectively answers」「Giving up and providing the answer after a few failed attempts」。
- `rodbv/socratic-skills`(16 星)https://github.com/rodbv/socratic-skills :`quiz-me` 在 commit 前讀 `git diff HEAD` 出 5 到 7 題考使用者,「A good question cannot be answered by copy-pasting a line from the source」。`guide-me` 每步只說要做什麼與介面是什麼,然後停住等使用者說 done 或 hint,刻意不公布完整步驟清單。
- **可借鏡**:用「禁止的具體句型」約束模型,比說「要蘇格拉底式」有效;把檢核掛在既有工作流關卡(commit 前);用 `git diff` 當客觀繳交證據,不信使用者口頭說「寫完了」。
- **不適用**:兩者都是純 prompt,Edit/Write 仍可用,規則只靠自律。「絕不給答案」與 Kestin 有證據的設計相反,且沒有效果量測。

### D5. GitHub Copilot 社群的 `mentor.agent.md` 【開源專案】

- URL:https://github.com/github/awesome-copilot/blob/main/agents/mentor.agent.md
- **做法**:frontmatter `tools:` 白名單全是唯讀工具,編輯工具根本不在清單裡,再配一句「Don't make any code edits」。
- **可借鏡**:教學模式應該連同工具權限一起降級成唯讀。這正是 D4 兩個 skill 缺的硬約束,也和 Khanmigo 的思路一致。

### D6. 空白

跑了兩組針對性查詢(「prediction-first」「predict then reveal」「make a prediction」+ tutor/prompt/skill),**沒有找到任何真正實作「先寫下預測、鎖住、才揭曉、比對差異」的專案。** 最接近的三個都是弱形式:Kestin 只擋第一則就要答案;`quiz-me` 是事後驗證不是事前預測;`TODO(human)` 是「你來寫」不是「你先猜」。這個空白就是本專案的差異點。

---

## 對話中的說法核對表

| ChatGPT 說了什麼 | 證據支持程度 | 來源 |
|---|---|---|
| 「確實有研究提到,遇到困難、卡關再解決,記憶點會更深」(未引來源) | **支持。** 合意困難與前測效應都有一手證據,d 0.45 到 1.5。 | A1 Bjork 1994/2011;A4 Richland 2009、Kornell 2009 |
| 「叫他只給提示不給答案,或把答案遮一半要你補完」 | **部分支持。** 「只給提示」在 Bastani 只抵銷傷害、無額外增益;Kestin 更完整的設計才贏課堂。「補完」對應生成效應。 | B1、B2;A3 Slamecka 1978 |
| 「每週留一天無 AI 練習日」 | **未證實。** 沒有研究測過這個處方。最接近的是 Bastani 的閉卷考才看得到落差。 | B1 |
| 「先試著自己消化半分鐘到一分鐘,還是不懂就直接問」 | **方向支持,數字是編的。** 前測效應支持「先試」,連猜錯都有效。「半分鐘到一分鐘」無出處。 | A4 |
| 「自己想、卡住、拿提示、再自述」的循環 | **支持。** 自我解釋 g = .55;而且先解釋後講解的順序有調節數據。 | A6 Bisra 2018 |
| 「AI 幫你整理得再好,判斷對錯還是你自己來」 | **相關性支持。** 對 AI 信心越高、自陳批判思考越少;批判思考轉向驗證與整合。 | B5 Lee 2025 |
| 使用者說:PISA 最近一次是歷年最大跌幅,專家猜與螢幕或 AI 有關。ChatGPT 搜尋一個網站後沒有回答這件事 | **跌幅屬實;AI 歸因在時間上不成立;ChatGPT 沒指出。** 施測 2022 年 4 月,ChatGPT 2022 年 11 月上線。OECD 說 COVID 只能部分解釋、跌勢早於 2018。螢幕:適度學習用途為正相關,分心為負相關。 | B3 |
| 「把操作的手外包,不要把『理解發生什麼』外包」 | **支持(相關與實驗混合)。** 用 AI 解釋概念者一週後增益仍在,用 AI 生成者消失;ChatGPT 組減少自我監控。 | B6 Contractor 2026、Fan 2025 |
| 「關掉 AI,白紙寫下:知道什麼、為什麼、條件改變會怎樣、怎麼證明」 | **支持。** 這就是提取練習;一週後 56% 對 42%。 | A2 |
| 「像教一個不懂的人,教得會就是理解了」 | **部分支持。** 「真的教了」在延遲測驗有效;只「預期要教」只在立即測驗有效。費曼技巧本身無一手出處。 | A7(Fiorella 為二手) |
| 「能不能講得跟 AI 一樣順,不是好的理解測試」 | **支持。** 答案在畫面上時的自評高估 15 個百分點;讀四次組信心最高、表現最差。 | A9 Koriat & Bjork 2005;A2 |
| 「先押一個答案再看,押得出來就代表在思考」 | **支持,可再加一步。** 前測效應;加收信心可利用超額修正。 | A4、A5 |
| 「想什麼自己寫,整理成什麼樣子交給 AI」 | **方向支持。** 寫自己的假設是生成;「整理成漂亮筆記」被 Dunlosky 評為低效用,交給 AI 正好。 | A3、A8 |
| 「不用無時無刻,一週一次深度一點」 | **分散練習支持「分散」,不支持「一週」這個數字。** 最佳間隔取決於保留目標。 | A8 Cepeda 2006 |
| 「請它出題你回答,再針對答案追問兩題」 | **支持。** 測驗效應;追問對應自我解釋提示。 | A2、A6 |
| 結尾:「思考本身是整理已知、對未知建立假設、用證據修正 mental model,而這些才是 AI 最容易取代的部分」 | **語意錯置,應為口誤。** 前後文顯示意思是「最不該被取代」。使用者的 take-away 4 已正確理解。 | 對話原文 |

---

## 設計含意

### 決策一:預測閘門用 hook 強制,還是用 prompt 提醒

- **選項 A,純 prompt。** skill 裡寫「揭曉前先要使用者寫預測」。優點:零摩擦、不會抹掉 prompt。缺點:Bastani 的 GPT Base 就是這種失敗:使用者說「直接告訴我」模型多半會從。memory.md 明說這種規則不是強制。
- **選項 B,command hook 檢查檔案。** `UserPromptSubmit` 檢查 `learning-records/current.md` 有沒有非空的「預測」段落,沒有就 block。優點:確定性、不依賴模型自律。缺點:block 會抹掉 prompt 且理由不進 context;30 秒逾時 fail-open;使用者可以敷衍通過。
- **建議:B 的輕量版,加上範圍限制。** hook 只檢查「預測段落存在且含信心欄位」,不判斷內容。真正防洩答的手段改用 Khanmigo 和 Copilot mentor 的思路:建 testcase 的 subagent 拿不到使用者的預測,主對話的 skill 在揭曉前只准輸出「證據」不准輸出「結論」。不要用 prompt/agent 型 hook 當裁判,判斷者也是 LLM,代價是每則 prompt 多一次呼叫且被擋時 prompt 消失。
- **要知道的一件事**:使用者自己想被推多硬。這是 `CLAUDE.local.md` 該記的偏好,也是 grill-me 該問的第一題。

### 決策二:skill 還是 subagent

- **事實**:非 fork 的 subagent 拿不到 `AskUserQuestion`,只能回摘要。流程七步裡有四步(寫已知、寫預測、自述解釋、比對)需要使用者輸入。
- **建議:主體是 skill,兩個環節用 subagent。** Skill 設 `disable-model-invocation: true`,由使用者一週一次手動 `/study` 開啟;內容常駐整段對話。建 testcase 跑實驗、出遷移題這兩步丟給 subagent,理由不是省 context,而是**盲測**:subagent 看不到使用者的預測與解釋,出的題不會被污染。
- **取捨**:subagent 不繼承 Learning output style,也不繼承 skill 的規則,每個 subagent 要自帶定義檔或用 `skills` 欄位預載。多一個定義檔要維護。
- **本機已有的東西**:`teach` skill 的檔案結構(MISSION、learning-records、NOTES)可直接沿用;`learning-companion` 的「不要問自評、用表現判斷程度」原則要保留;新 skill 補的缺口是「預測鎖定後才揭曉」與「跑實驗拿證據」這兩步,現有兩個 skill 都沒有。

### 決策三:揭曉時機

- **選項 A,絕不給答案**(bevibing、rodbv guide-me 的做法)。沒有效果證據,且與 Kornell 2009「猜完立即給正解」的實驗條件相反。
- **選項 B,預測鎖定後立即揭曉,再要求解釋差異。** Kornell 與 Richland 的所有實驗都是這樣做的;Kestin 允許確認答案與最終揭曉;超額修正需要「看到正解」才發生。
- **建議:B。** 閘門擋的是「還沒預測就要答案」,不是「永遠不給答案」。揭曉後緊接兩件事:使用者用自己的話解釋為什麼跟預測不一樣(Bisra:解釋之前不給講解),以及標出「高信心且錯」的項目優先回顧。
- **另外三條從證據來的小規則**:解釋還在螢幕上時不問「懂了嗎」(A9);遷移題用開放式不用選擇題(Bisra 選擇題 g = .24 不顯著);下次開場先抽問上次紀錄(A8,分散)。

### 紀錄檔的最小欄位

從 A 節反推,每次紀錄至少要有:主題與範圍;已知(使用者寫);問題;假設;預測與信心(高/中/低);實驗指令與產物路徑(AI 寫);結果與預測的差異;使用者的解釋(使用者寫);遷移題與作答;下次要抽問的一句話。用 `teach` 的 `learning-records/NNNN-<slug>.md` 編號慣例。

---

## 沒查到或沒確認的事

依重要性排序。

1. **Butterfield & Metcalfe (2001) 正文與任何數字**:付費牆。流傳的 gamma = .36 無法確認。
2. **PNAS 對 Bastani 的更正(pnas.2518204122, https://www.pnas.org/doi/10.1073/pnas.2518204122 )**:PNAS 站 403,內容未取得,不知道更正了什麼。
3. **PISA 2025 「每日用 AI 者低 28 分」等具體分數**:只在新聞看到,Vol. I 正文只有方向性敘述,數字可能在圖表裡。新聞稿頁面 403。
4. **Fiorella & Mayer (2013)、Nestojko (2014) 的效果量**:付費牆或只有摘要。
5. **Chi (1989) 正文**:數字為 Chi 本人 1994 年自述。
6. **Kosmyna 是否已有期刊版**:截至今天搜不到,v2 仍標 under review。
7. **Claude Code 的 Learning output style 內建 prompt 全文**:未公開;plugin 版自述與內建版不同。
8. **`TODO(human)` 的實作機制**:文件只有一句。
9. **Fork 能否向使用者提問**:從「Forks skip both filters」推論為可以,文件沒明說。
10. **Claude for Education Learning mode 的實作**:官方公告 https://www.anthropic.com/news/introducing-claude-for-education 只有兩句提問樣板,沒有 prompt 或規則;「2025-08 開放全體使用者」只有二手新聞。
11. **Khanmigo 的完整規則**:只公開「限制 agent 範圍」這一條。
12. **Cepeda「最佳間隔是保留期的 10 到 20%」**:應為 Cepeda et al. (2008) *Psychological Science* 19(11),本次未核對。
13. **Stanford / CMU 主導的 LLM 學習 RCT**:沒找到符合的一手論文。
14. **Gerlich 2025 認知卸載研究**(*Societies* 15(1):6, https://www.mdpi.com/2075-4698/15/1/6 ):MDPI 403,且有已發布更正,未列入。
15. **獨立查核的範圍**:一個沒看過前文的 opus agent 實際打開來源核對了 12 個主張(A2、A4 兩項、A6、B1、B2、B3 兩項、C1 的 AskUserQuestion 過濾、C2 的 block 抹掉 prompt、C3 的「不是強制設定」、D3 的 50%),全部相符。未獨立核對的:C 節引用的文件行號、hooks JSON 巢狀層級的細節(本文不依賴)、A1/A3/A5/A8/A9、B4 到 B6、D1/D2/D4/D5 的引文。
