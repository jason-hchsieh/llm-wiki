# A. 學習科學一手來源查核

查核日期：2026-09-19。方法：每一條都實際抓取來源檔案（多數為 PDF，於本機以 pypdf 抽文字後逐句核對），數字一律抄自論文本文或摘要，未從部落格轉引。轉引之處都有明說。

卷期頁碼：PDF 本身未顯示卷期頁碼者，以 Crossref API（`api.crossref.org/works/<DOI>` 或依標題查詢）逐筆核對後填入，未憑記憶書寫。唯一例外是 Bjork & Bjork (2011) 章節無 DOI，故只保留 PDF 頁眉可見的資訊。

**證據等級標籤**：【同儕審查】【預印本】【工作論文】【官方報告】【新聞/部落格】【二手】【未證實】

**名詞速查**（第一次出現處也會就地解釋）
- **效果量 (effect size)**：用來描述「差距有多大」的標準化數字，與樣本數無關。常見的 Cohen's *d* 與 Hedges' *g* 大致可讀作：0.2 小、0.5 中、0.8 大。
- **保留間隔 (retention interval, RI)**：從學完到最終測驗之間隔了多久。
- **學習間隔 (inter-study interval, ISI)**：同一份材料兩次學習之間隔了多久。

---

## 1. Desirable difficulties（合意困難）

> **合意困難**：指在學習當下會讓人表現變差、感覺更吃力，但長期保留與遷移反而更好的訓練操作。

### 1a. Bjork (1994) — 概念原始出處

1. **來源與作者年份**
   - Bjork, R. A. (1994). *Memory and metamemory considerations in the training of human beings.* In J. Metcalfe & A. Shimamura (Eds.), **Metacognition: Knowing about Knowing** (pp. 185–205). Cambridge, MA: MIT Press.
   - 實際抓到並逐頁核對的全文 PDF：https://gwern.net/doc/psychology/spaced-repetition/1994-bjork.pdf （PDF 首頁自帶上述完整書目資訊，確認無誤）
   - 標籤：【同儕審查】（MIT Press 學術專書之編審章節；非期刊同儕審查，屬編輯審查的學術章節——嚴格說是「學術專書章節」，本檔為求標籤一致歸入【同儕審查】並在此註明差異）
2. **研究設計一句話**：這不是實驗報告，而是一篇回顧／理論章節，整理當時的訓練與記憶文獻，主張「有效的訓練操作大多有個共同性質：它們替學習者製造困難」。
3. **關鍵數字與原文用語**
   - 章節正文中明確出現 "the types of **desirable difficulties** summarized in the preceding section"（p.193 前後），前一節列出的五類操作為：**varying the conditions of practice**（變動練習情境）、**providing contextual interference**（製造情境干擾，含交錯練習）、**distributing practice**（分散練習）、**reducing feedback to the learner**（降低回饋頻率）、**using tests as learning events**（把測驗當學習事件）。
   - 章節內引述的西班牙文字彙訓練資料（Bahrick 式設計）：訓練節次間隔 0 天／1 天／30 天，在**最後一次訓練後 30 天**測驗，回憶率分別為 **33%、64%、72%**——但在訓練期間的表現剛好相反（0 天最好）。原文：「the levels of recall were dramatically reversed, with the 30-day spacing of training sessions yielding clearly superior recall (72% after three training sessions, versus 33% and 64% in the 0-day and 1-day conditions, respectively)」。
   - 本章未報告效果量。
4. **對 AI 共學的設計含意**：AI 最自然的失敗模式是「把事情變簡單」——秒答、補完、給範例。這章的主張是：系統該有意識地保留一部分困難（讓學習者先試、延後回饋、換情境問），並且要向使用者解釋「現在變難是刻意的」，否則使用者會用「當下流暢度」判斷產品好壞。

### 1b. Bjork & Bjork (2011) — 最常被引用的科普化版本

1. **來源與作者年份**
   - Bjork, E. L., & Bjork, R. A. (2011). *Making things hard on yourself, but in a good way: Creating desirable difficulties to enhance learning.* In **Psychology and the Real World**, Chapter 5, pp. 55–64.（編者與出版社**未**核對，故此處不列；PDF 頁眉僅顯示書名、章次與頁碼）
   - 作者實驗室網站上的全文 PDF（已抓取確認）：https://bjorklab.psych.ucla.edu/wp-content/uploads/sites/13/2016/07/EBjork_RBjork_2011.pdf
   - 作者網頁本身：https://bjorklab.psych.ucla.edu/ （已抓取；首頁自述為 "how people learn, versus how they think they learn"，但首頁**沒有**publications 清單頁，也沒有 desirable difficulties 的定義段落——PDF 是掛在 wp-content 下的檔案）
   - 標籤：【同儕審查】（編審學術專書章節，同上註記）
2. **研究設計一句話**：回顧型章節，以訪談問答體裁介紹「儲存強度 vs. 提取強度」理論，並整理四類合意困難的實驗證據。
3. **關鍵數字（皆抄自章節本文）**
   - 四類合意困難：varying the conditions of learning、interleaving（交錯）、spacing（分散）、using tests rather than presentations as study events。
   - **交錯練習**：學習多種立體（如截頂圓錐）體積公式，交錯組 vs. 集中組在一週後新題正確率為 **63% 對 20%**（章節引 Rohrer & Taylor, 2007）。
   - **歸納學習的交錯**：學 12 位畫家風格各 6 幅畫，交錯呈現比集中呈現更能辨認新畫作者；但**多數受試者事後仍認為集中比較有效**，與實際學習結果相反（引 Kornell & Bjork, 2008）。
   - **變動練習情境**：8 歲與 12 歲兒童蒙眼投沙包，固定距離練習組 vs. 變動距離練習組，最後**在固定組練過的那個距離上測驗**，變動組反而較佳（引 Kerr & Booth, 1978）。章節未給百分比。
   - 章節中**沒有**報告統整的效果量。
   - 重要提醒（原文）：「If … the learner does not have the background knowledge or skills to respond to them successfully, they become **undesirable** difficulties.」
4. **對 AI 共學的設計含意**：困難必須「可應付」。系統需要一個難度調節器：在學習者具備前置知識時才插入交錯／變動／延遲，否則同一操作會從合意困難變成純粹挫折。另外，「使用者主觀評分」不能當作學習效果的代理指標——交錯那一條實驗直接顯示兩者反向。

---

## 2. Retrieval practice / testing effect（提取練習／測驗效應）

> **測驗效應**：把「考自己」當成學習事件（而不只是評量），比同樣時間重讀更能提升長期保留。

1. **來源與作者年份**
   - Roediger, H. L., III, & Karpicke, J. D. (2006). *Test-enhanced learning: Taking memory tests improves long-term retention.* **Psychological Science, 17**(3), 249–255. DOI: 10.1111/j.1467-9280.2006.01693.x
   - 抓取並逐頁核對的全文 PDF：https://colinallen.dnsalias.org/Readings/2006_Roediger_Karpicke_PsychSci.pdf
   - 期刊頁：https://journals.sagepub.com/doi/abs/10.1111/j.1467-9280.2006.01693.x ；PubMed：https://pubmed.ncbi.nlm.nih.gov/16507066/
   - 標籤：【同儕審查】
2. **研究設計一句話**：受試者讀科普短文後，或重複「讀」、或重複「自由回憶測驗（無回饋）」，再於 5 分鐘／2 天／1 週後做最終自由回憶測驗。
3. **關鍵數字（全部抄自論文 Results 段）**
   - **實驗 1**（讀一次後，再讀 vs. 再測一次）
     - 5 分鐘：重讀 **81%** > 測驗 **75%**，t(39)=3.22, *d*=0.52
     - 2 天：測驗 **68%** > 重讀 **54%**，t(39)=6.97, *d*=0.95
     - **1 週：測驗 56% > 重讀 42%**，t(39)=6.41, *d*=0.83 ← 題目要的「1 週後回憶率」就是這組
     - 論文原話：初測組在 **1 週後**的回憶量，等同（實際上略高於）重讀組在**只隔 2 天**時的表現——「taking an initial recall test prevented forgetting of information for an additional 5 days relative to repeated study」。
     - 變異數分析：學習條件主效果 F(1,117)=36.39, ηp²=.24；保留間隔主效果 F(2,117)=50.34, ηp²=.46；交互作用 F=32.10, ηp²=.35。
   - **實驗 2**（SSSS 讀四次 / SSST 讀三次+測一次 / STTT 讀一次+測三次）
     - 5 分鐘：SSSS **83%** > SSST **78%** > STTT **71%**
     - **1 週：STTT 61% > SSST 56% > SSSS 40%**
     - 一週間的遺忘量：SSSS 忘掉 **52%**、SSST **28%**、STTT **14%**
     - 交互作用 F(2,174)=18.48, ηp²=.18；1 週時 STTT vs SSSS 的 *d*=1.26
     - **元認知反差**：SSSS 組對「一週後我記得住」的信心**最高**，F(2,177)=6.09，但實際表現最差。
4. **對 AI 共學的設計含意**：產品的預設互動不該是「AI 講解 → 使用者點頭」，而該是「AI 先問 → 使用者回答 → 才給解答」；而且效益只在延遲測驗才看得到，所以評估自家產品時必須量測隔天／隔週的表現，當堂立即測驗會系統性地讓「重讀式」介面看起來更好。同時，使用者的「我覺得我懂了」與實際保留是反向的，不能當成產品指標。

---

## 3. Generation effect（生成效應）

> **生成效應**：自己產出答案（即使只是補一個字）比直接讀現成答案記得更牢。

1. **來源與作者年份**
   - Slamecka, N. J., & Graf, P. (1978). *The generation effect: Delineation of a phenomenon.* **Journal of Experimental Psychology: Human Learning and Memory, 4**(6), 592–604.
   - 抓取並核對的全文 PDF（掃描版，OCR 品質差但可讀）：https://andymatuschak.org/prompts/Slamecka1978.pdf
   - 標籤：【同儕審查】
2. **研究設計一句話**：五個實驗，比較「自己依規則生成目標詞」（如看到 `rapid-f` 要說出 `fast`）與「直接讀出整組詞」（`rapid-fast`）兩種編碼方式，用再認、自由回憶、線索回憶與信心評分測量。
3. **關鍵數字（抄自論文本文；部分主要均值只畫在圖上，論文正文未列出）**
   - 實驗 1（再認）：generate 顯著優於 read，F(1,20)=9.68, MSe=.07；五種語意規則（associate / category / opposite / synonym / rhyme）之間**無**交互作用 F(4,80)=2.09 ns，即效應不隨規則改變。計時 vs 自訂步調兩組整體再認率 .75 與 .79，無差異（F<1）。
   - 實驗 2：generate 優勢重現，F(1,10)=27.17, MSe=.04。
   - 實驗 4（押韻材料，再認）：generate vs read 主效果 F(1,20)=26.19；且優勢**只出現在反應詞（response）而非刺激詞（stimulus）**，交互作用 F(1,20)=10.75。
   - 實驗 5（線索回憶，五次學習—測驗循環）：跨五次 trial 平均，**反應詞回憶 generate .68 vs read .61**；對照之下刺激詞回憶 generate .53 vs read .51（論文原話：差距「more than 3 times greater」）。generate vs read 主效果 F(1,22)=6.19。
   - 論文**未**報告 Cohen's *d* 類效果量（1978 年的慣例）。
   - ⚠️ 實驗 1 的 generate/read 分項百分比只存在於 Figure 1，正文沒抄出來，我也沒有從圖上目測數字。
4. **對 AI 共學的設計含意**：把「填空／補完」下放給使用者，而不是由 AI 一次輸出完整句子。注意實驗 4 的界線：優勢落在**被生成的那一項**，所以要讓使用者生成的，必須正好是你希望他記住的那個詞或那一步，而不是周邊敘述。

---

## 4. Prediction-before-reveal / pretesting（先猜再看／前測效應）

> **前測效應**：在還沒讀到答案之前先被問一次（即使答錯），之後學同樣內容會學得更好。

### 4a. Richland, Kornell & Kao (2009)

1. **來源與作者年份**
   - Richland, L. E., Kornell, N., & Kao, L. S. (2009). *The pretesting effect: Do unsuccessful retrieval attempts enhance learning?* **Journal of Experimental Psychology: Applied, 15**(3), 243–257.（卷期頁碼經 Crossref 核對）
   - 抓取並核對的全文 PDF（作者實驗室）：https://learninglab.uchicago.edu/Pre-Testing_files/RichlandKornellKao.pdf
   - PubMed：https://pubmed.ncbi.nlm.nih.gov/19751074/ ；ERIC：https://eric.ed.gov/?id=EJ859957
   - 標籤：【同儕審查】
2. **研究設計一句話**：五個實驗，受試者閱讀一篇關於視覺的科普文章，前測組在閱讀前先被問文中概念（幾乎必錯），延長學習組則多給閱讀時間；分析**只納入前測答錯的題目**。
3. **關鍵數字（抄自各實驗 Results 段）**
   - **實驗 1**：前測組最終測驗 **75%**（SE 3.2）vs 延長學習組 **56%**（SE 2.7），t(61)=4.26, p<.0001, ***d*=1.1**。前測時受試者答錯率達 **95%**。同一組內：被測過的題 75% vs 未測過的題 50%，t(35)=5.03, *d*=1.7。
   - **實驗 2**（對照組用斜體標出同樣概念，以排除「注意力導向」解釋）：**71%**（SE 5.6）vs **53%**（SE 4.3），t(32)=3.27, p<.003, *d*=0.63。
   - **實驗 4**（**延遲一週**測驗、用粗體關鍵字當對照）：**55%**（SE 2.0）vs **45%**（SE 3.0），t(156)=2.8, p<.0025, *d*=0.45。
   - **實驗 5**（只呈現問題、不要求作答）：前測組 **78%**（SE 4.2）vs 延長學習組 **63%**（SE 5.9）。
4. **對 AI 共學的設計含意**：在 AI 解釋任何新概念之前，先丟一個使用者幾乎一定答不出的問題，是有實驗支持的預設動作；而且它的效益不是來自「把注意力標紅」——實驗 2、3 用斜體／粗體當對照仍然輸給前測。實務上要處理的是體驗問題：95% 答錯率需要產品語言把它框成「這題本來就是要你猜的」。

### 4b. Kornell, Hays & Bjork (2009)

1. **來源與作者年份**
   - Kornell, N., Hays, M. J., & Bjork, R. A. (2009). *Unsuccessful retrieval attempts enhance subsequent learning.* **Journal of Experimental Psychology: Learning, Memory, and Cognition, 35**(4), 989–998. DOI: 10.1037/a0015729
   - 抓取並核對的全文 PDF：https://web.williams.edu/Psychology/Faculty/Kornell/Publications/Kornell.Hays.Bjork.2009.pdf
   - 標籤：【同儕審查】
2. **研究設計一句話**：六個實驗，用「保證答不出來」的材料（虛構常識題如 "What peace treaty ended the Calumet War?"，或弱聯想詞對如 whale → mammal），比較「先猜再看答案」與「問題與答案同時呈現」，並把少數猜對的 trial 剔除。
3. **關鍵數字（抄自各實驗 Results 段）**
   - 實驗 1（虛構常識題）：測驗組 **.41**（SD .21）vs 純閱讀組 **.31**（SD .17），t(24)=2.97, p<.01, *d*=0.58。
   - 實驗 3（弱聯想詞對）：**.71**（SD .20）vs **.50**（SD .19），t(14)=5.77, p<.0001, ***d*=1.49**。
   - 實驗 4（兩組總時間都 13 秒，嚴格等時）：**.67** vs **.55**，t(14)=3.20, p<.01, *d*=0.38。
   - 實驗 5（**延遲平均 38 小時**）：**.47**（SD .22）vs **.35**（SD .17），t(29)=5.16, p<.0001, *d*=0.94；論文明說優勢幅度約 **12 個百分點**，與 5 分鐘延遲的實驗 4 相當。
   - 實驗 6（受試者間設計，排除「選擇性複誦」解釋）：**.69** vs **.60**，t(82)=2.04, p<.05, *d*=0.44。
   - 猜錯與完全空白的後續學習成效**沒有顯著差異**（例如實驗 5：留白 .51 vs 寫錯 .44, t(22)=0.84, p=.41）。
4. **對 AI 共學的設計含意**：「猜了但錯」與「想不出來」在後續學習上一樣有效，所以介面不需要逼使用者一定要打出答案；讓他按一下「我想不出來」也保留了大部分效益。但必須**緊接著給正確答案**——這些實驗裡猜測後一律立即顯示答案。

---

## 5. Hypercorrection（高信心錯誤的超額修正）

> **超額修正效應**：人答錯時如果本來很有把握，看到正解後反而**更容易**改對；而不是像直覺預期的那樣更難改。

1. **來源與作者年份**
   - Butterfield, B., & Metcalfe, J. (2001). *Errors committed with high confidence are hypercorrected.* **Journal of Experimental Psychology: Learning, Memory, and Cognition, 27**(6), 1491–1494. DOI: 10.1037//0278-7393.27.6.1491
   - 取得途徑：全文為付費牆，我**沒有**拿到正文。抓到的是期刊登錄之官方摘要（Europe PMC / PubMed 書目紀錄，含期刊提供之 abstractText）：https://pubmed.ncbi.nlm.nih.gov/11713883/ （程式化取得自 https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=EXT_ID:11713883&resultType=core ）
   - 標籤：【同儕審查】（但**只有摘要層級**的一手確認，見第 3 點警告）
2. **研究設計一句話**：受試者回答一般常識題並對每題答案評信心，答錯者看到正確答案作為回饋，稍後就同一批題目再測一次。
3. **關鍵數字**
   - 官方摘要原文關鍵句（逐字）：「In contrast to the authors' expectations, **highly confident errors were the most likely to be corrected in a subsequent retest.** Participants nearly always endorsed the correct response in cases in which both the correct response and the original erroneous response were generated at retest…」
   - ⚠️ **摘要中沒有任何數字**。網路上廣傳的「mean gamma G = .36」我**無法**在這篇 2001 年論文中確認。
   - 我另外抓到同組作者的 Butterfield & Metcalfe (2006), *The correction of errors committed with high confidence*, **Metacognition and Learning, 1**, 69–84（全文 PDF：http://www.columbia.edu/cu/psychology/metcalfe/PDFs/Butterfield_Metcalfe_2006.pdf ，【同儕審查】）。該文重新分析的資料在其參考文獻中標為 **Butterfield & Metcalfe (2001b) "Updating the egregious"**，與 2001 年 JEP:LMC 那篇（該文標為 **2001a**）**是不同的研究**。因此 2006 年文中的表 1 條件機率與 gamma 值**不能**直接當成 2001a 的數字。
   - 可確認來自 2006 年論文本身的數字：信心與**初測**正確率的平均 gamma 相關為 **.63**（兩個實驗皆同）；控制常模題目難度後，信心與改對之偏相關仍顯著，*pr*=.11, t(68)=3.70, p<.001；跨題目分析 gamma=.22, t(63)=2.08, p<.05。也就是說，超額修正**不能**僅用「題目本來就比較簡單」解釋。
4. **對 AI 共學的設計含意**：使用者最篤定、結果卻錯的那一題，是最值得花版面糾正的一題——不是最該迴避的。可行做法：作答時一併收信心（一個滑桿或三選一），把「高信心 × 錯」標成優先回顧佇列，並在回饋時放大「意外感」（明確指出「這跟你剛才的把握程度不一樣」），因為 2006 那篇的注意力假說正是靠這個機制解釋效應。

---

## 6. Self-explanation（自我解釋）

> **自我解釋**：學習者一邊讀一邊對自己說明「為什麼是這樣」「這一步和前一步的關係」，而不是複誦字面內容。

### 6a. Chi, Bassok, Lewis, Reimann & Glaser (1989) — 原始觀察研究

1. **來源與作者年份**
   - Chi, M. T. H., Bassok, M., Lewis, M. W., Reimann, P., & Glaser, R. (1989). *Self-explanations: How students study and use examples in learning to solve problems.* **Cognitive Science, 13**(2), 145–182. DOI: 10.1207/s15516709cog1302_1
   - 期刊頁（已由 Semantic Scholar API 確認書目與 DOI）：https://onlinelibrary.wiley.com/doi/abs/10.1207/s15516709cog1302_1
   - ⚠️ Wiley 與 ScienceDirect 都對我回 403，我**沒有**拿到 1989 年這篇的正文 PDF。
   - 標籤：【同儕審查】（書目確認；數字為作者本人在 1994 年論文中的自述，見下）
2. **研究設計一句話**：8 名大學生先讀力學課文，再一邊出聲一邊研讀三個解題範例，事後解章末習題；依解題表現分成「好」「差」兩組，回頭比對他們出聲protocol 中的自我解釋量。
3. **關鍵數字**
   - 數字來源是 **Chi et al. (1994) 第 440 頁**對自己 1989 年研究的敘述（我抓到 1994 年全文 PDF 並逐字核對）：「The 4 students who were subsequently more successful at solving problems at the end of the chapter (**averaging 82% correct in the posttest**) were the ones who spontaneously generated a greater number of self-explanations while studying the examples (**15.3 explanations per example**). The 4 less successful students **averaged 46% correct** on the posttest and generated only **2.8 explanations per example** (Chi et al., 1989).」
   - 標籤修正：這四個數字就我的查核而言是【二手】——雖是同一位第一作者的自述，仍非從 1989 年原文抄出。
   - 研究設計上請注意：1989 年這篇是**相關性／觀察**研究（自發解釋量 vs 成績），**不是**隨機分派實驗，因此不能單獨支持因果宣稱。
4. **對 AI 共學的設計含意**：「解釋量」本身不是操弄變項，所以別把 1989 當成「要求解釋就會變好」的證據；它提供的是一個可量測的行為訊號——AI 可以偵測使用者的敘述是在複誦還是在推理（是否出現條件、因果、與原理的連結），並據此決定要不要介入。

### 6b. Chi, de Leeuw, Chiu & LaVancher (1994) — 隨機分派的因果證據

1. **來源與作者年份**
   - Chi, M. T. H., de Leeuw, N., Chiu, M.-H., & LaVancher, C. (1994). *Eliciting self-explanations improves understanding.* **Cognitive Science, 18**(3), 439–477.（卷期頁碼經 Crossref 核對）
   - 抓取並核對的全文 PDF：https://andymatuschak.org/files/papers/Chi%20et%20al%20-%201994%20-%20Eliciting%20self-explanations%20improves%20understanding.pdf
   - 標籤：【同儕審查】
2. **研究設計一句話**：14 名八年級生讀人體循環系統課文時，每讀一句就被要求自我解釋；10 名對照組把同一篇課文讀兩遍（時間大致相當）；前後測比較理解程度。
3. **關鍵數字（抄自論文本文）**
   - 前測→後測的增幅：**被提示組 32% vs 對照組 22%**，交互作用 F(1,22)=5.1, p<.05。
   - 只看較難的第 3、4 類題（需要推論與常識知識）：**被提示組進步 22.6% vs 對照組 12.5%**，t(22)=2.64, p<.01。
   - 兩組整體都顯著進步 F(1,22)=183.6, p<.001（即對照組也有學到，差別在增幅）。
   - 組間學習時間：被提示組平均與對照組 1 小時 6 分大致相當（對照組範圍 22 分–2 小時 47 分）。
   - 質性結果：高解釋量者**全部**建立了正確的循環系統心智模型，低解釋量者與未提示者多半沒有。
4. **對 AI 共學的設計含意**：「每讀一句就提示解釋」這個極簡操作（無訓練、無客製）就拿到了效果，而且效益集中在**需要推論的難題**上。所以 AI 的解釋提示該優先掛在概念交界處，而不是每一個事實後面；評估時也要用需要推論的題目，用字面回憶題會低估效果。

### 6c. Bisra, Liu, Nesbit, Salimi & Winne (2018) — 後設分析效果量

1. **來源與作者年份**
   - Bisra, K., Liu, Q., Nesbit, J. C., Salimi, F., & Winne, P. H. (2018). *Inducing self-explanation: A meta-analysis.* **Educational Psychology Review, 30**(3), 703–725.（卷期頁碼經 Crossref 核對） DOI: 10.1007/s10648-018-9434-x
   - 抓取並核對的全文 PDF：https://gwern.net/doc/psychology/spaced-repetition/2018-bisra.pdf ；期刊頁：https://link.springer.com/article/10.1007/s10648-018-9434-x
   - 標籤：【同儕審查】
2. **研究設計一句話**：後設分析（把多項研究的效果量合併計算），收錄「有沒有給自我解釋提示」的對照研究。
3. **關鍵數字（抄自摘要與表 1、表 2）**
   - 收錄 **69 個效果量、來自 64 份研究報告、共 5,917 名受試者**。
   - 隨機效果模型整體 **Hedges' *g* = .552**（SE .050），**95% CI [.454, .650]**，p<.001。
   - 異質性 Q=196.63 (df=68, p<.001)，**I² = 65.42**（即效果大小差異有約 65% 不是抽樣誤差造成）。
   - **對照組類型是顯著調節變項**（Q_B=11.648, df=3, p=.009）：
     - 對照組沒有額外教學：*g* = **.673**（41 個效果量）
     - 對照組給「現成講解」(instructional explanation)：*g* = **.354**（6 個）
     - 對照組給別的學習策略：*g* = **.304**（7 個）
     - 事後檢定：無額外教學顯著大於現成講解（z=2.696, p=.007）與其他策略（z=3.119, p=.002）。
   - 提示形式：填空 *g*=.895（僅 2 個效果量）、預先指定 .700、疑問句 .559、祈使句 .395、**選擇題 .242（不顯著，95% CI 含 0）**。
   - 引發的解釋類型：概念化 (conceptualize) *g*=.873、解釋 .678、辯護 (justify) .416、**後設認知式 .192（不顯著）**。
   - 作者在摘要結尾明白建議：「we recommend that future research explore **computer-generation of self-explanation prompts**」。
4. **對 AI 共學的設計含意**：這是本次查到最直接可用的數字。(a) 自我解釋提示 *g*≈.55 屬中等效果；(b) 但只要 AI 順手把現成講解也給了，效果就從 .67 掉到 .35——**先提示解釋、後給講解**的順序是有數據支撐的；(c) 用選擇題當解釋提示幾乎沒效，要用開放式或填空式；(d) 提示要引導「概念化／說明機制」，不要問「你覺得自己懂了嗎」這種後設認知問題（*g*=.192 且不顯著）。

---

## 7. Feynman technique（費曼技巧）

1. **一手來源：沒查到。**
   - 我搜尋了「Feynman 本人是否寫下此法」，**沒有**找到任何 Feynman 本人的著作、講稿或檔案提出這個四步驟方法。Feynman 過世於 1988 年。
   - 可找到的網路說法（多個科普站點一致宣稱）："Feynman never wrote this method down"、"the earliest trace of the name is from 2011"。這些站點彼此轉引、**沒有**指向可查證的檔案。
   - 標籤：**【未證實】**（「這是 Feynman 提出的方法」此一歸屬）／**【二手】**（作為一個流行的學習法描述）。
   - 相對可查證、但仍屬**傳記（二手）**的材料：James Gleick, *Genius: The Life and Science of Richard Feynman* 記載 Feynman 有一本標題為 "Notebook of things I don't know about" 的筆記本，並描述他逐一拆解物理各分支尋找「粗糙邊緣與不一致」。維基頁：https://en.wikipedia.org/wiki/Genius:_The_Life_and_Science_of_Richard_Feynman 標籤：【二手】。這**支持**「Feynman 有系統地盤點自己的無知」，但**不等於**那個四步驟流程。
2. **最接近且有證據的構念**：把費曼技巧拆開，其中有實證支撐的成分是 **self-explanation**（見第 6 項，*g*=.55）與 **learning by teaching / teaching expectancy**（見下）。「找出卡住的地方、回去補、用簡單語言重講」這三件事分別對應 self-explanation 的監控功能、提取練習與生成效應——這些都有一手證據；**缺的是「這個組合包本身」的直接證據**。

### 7a. Nestojko, Bui, Kornell & Bjork (2014) — 「預期要教」

1. **來源與作者年份**
   - Nestojko, J. F., Bui, D. C., Kornell, N., & Bjork, E. L. (2014). *Expecting to teach enhances learning and organization of knowledge in free recall of text passages.* **Memory & Cognition, 42**(7), 1038–1048. DOI: 10.3758/s13421-014-0416-z ；PMID 24845756
   - 期刊頁：https://link.springer.com/article/10.3758/s13421-014-0416-z
   - ⚠️ 全文 PDF（Williams 學院鏡像）對我回 403；我取得的是期刊提供之官方摘要（透過 Europe PMC REST API，EXT_ID:24845756）。
   - 標籤：【同儕審查】（**僅摘要層級**的一手確認）
2. **研究設計一句話**：兩個實驗，受試者讀同一篇文章，一組被告知稍後要**接受測驗**，另一組被告知要**教給另一位學生**（實際上所有人都只是受測，沒有人真的教）。
3. **關鍵數字**
   - 官方摘要逐字：「Participants expecting to teach produced **more complete and better organized free recall** of the passage (Experiment 1) and, in general, **correctly answered more questions** about the passage than did participants expecting a test (Experiment 1), particularly **questions covering main points** (Experiment 2)…」
   - ⚠️ 摘要**沒有**給出任何百分比、t/F 值或效果量；我**沒有**取得正文，因此無法提供數字。
4. **對 AI 共學的設計含意**：純粹改變「你等下要把這個教給別人」這句框架語就可能改變學習策略，成本近乎為零，值得做成一個可切換的模式（例如讓 AI 扮演需要被教會的學習者）。但由於我只確認到摘要層級、且無效果量，導入時應視為**待驗證的低成本假設**，並自行 A/B 量測。

### 7b. Fiorella & Mayer (2013) — 「預期要教」vs「真的教」

1. **來源與作者年份**
   - Fiorella, L., & Mayer, R. E. (2013). *The relative benefits of learning by teaching and teaching expectancy.* **Contemporary Educational Psychology, 38**(4), 281–288. DOI: 10.1016/j.cedpsych.2013.06.001（書目經 Crossref 與 Semantic Scholar API 確認）
   - 期刊頁：https://www.sciencedirect.com/science/article/abs/pii/S0361476X13000209
   - ⚠️ 全文與摘要皆付費牆（ScienceDirect 403、Crossref 無摘要、Semantic Scholar 摘要被出版社抽掉）。我**沒有**取得正文或摘要原文。
   - 標籤：【同儕審查】（僅書目確認）
2. **研究設計一句話（轉述自作者本人 2023 年的回顧章節，非原文）**：大學生學習「都卜勒效應」課文，操弄「預期要教 vs 預期受測」，其中部分預期要教者**實際錄製一段教學影片**，再比較立即與延遲的理解測驗。
3. **關鍵數字：沒查到。**
   - 我能核對到的最接近敘述，來自 **Fiorella 本人撰寫的章節** "Learning by Teaching"（收於 *In Their Own Words*，UNH 教學資源中心公開 PDF，已抓取並逐字核對）：https://www.unh.edu/teaching-learning-resource-hub/sites/default/files/media/2023-06/itow-learning-by-teaching-fiorella.pdf 標籤：【二手】（作者本人撰寫的回顧章節）
   - 該章節原文：「In a series of studies by Fiorella and Mayer (2013, 2014), undergraduate students who prepared to teach a lesson on the Doppler effect performed better on an **immediate** comprehension test, **but not a delayed** comprehension test, compared to students who prepared normally for a test.」以及「students who **actually taught** performed best on a **delayed** comprehension test」。
   - 同章節引用的後設分析（我**未**另行核對原文）：Kobayashi (2019) 跨 **28 項研究**，preparing-to-teach 相對 preparing-for-test 的平均效果量 ***d* = .30–.40**（小到中等）。標籤：【二手】。
4. **對 AI 共學的設計含意**：「預期要教」只買到立即測驗的好處；要拿到延遲保留，必須讓使用者**真的產出一段解釋**（口說或錄影／打字給一個假想聽眾）。對 AI 產品而言這是個明確的分界：光是告訴使用者「等下要教喔」不夠，要有一個實際的輸出環節，並且（依 6c 的調節結果）在他輸出之前不要先餵標準講解。

---

## 8. Spacing（分散練習）

### 8a. Cepeda, Pashler, Vul, Wixted & Rohrer (2006) — 後設分析

1. **來源與作者年份**
   - Cepeda, N. J., Pashler, H., Vul, E., Wixted, J. T., & Rohrer, D. (2006). *Distributed practice in verbal recall tasks: A review and quantitative synthesis.* **Psychological Bulletin, 132**(3), 354–380.
   - 抓取並核對的全文 PDF：https://augmentingcognition.com/assets/Cepeda2006.pdf ；作者列表頁：https://www.yorku.ca/ncepeda/publications/CPVWR2006.html
   - 標籤：【同儕審查】
2. **研究設計一句話**：後設分析／量化綜整，把語文回憶作業中的分散練習研究依「學習間隔 ISI」與「保留間隔 RI」兩個時間變項重新切分比較。
3. **關鍵數字（抄自摘要與 Results 段）**
   - 收錄規模：**839 筆分散練習評估、317 個實驗、184 篇文章**（自 427 篇篩選而來，共 958 個正確率數值）。
   - 間隔 vs 集中：共 **271 組**比較，其中**只有 12 組**顯示無效果或負向效果——論文原話「making the spacing effect quite robust」。
   - 保留間隔 < 1 分鐘時，分散相對集中提升最終測驗表現 **9 個百分點**；論文明說在從 <1 分鐘到 >30 天的七個保留間隔區間內，**沒有任何一個區間**集中優於分散。
   - 核心結論（**非單調的 lag 效應**）：對每個保留間隔，都存在一個使正確率最大的非零 ISI，且**最佳 ISI 隨保留間隔拉長而拉長**。論文舉例：保留間隔 <1 分鐘時，最佳 ISI <1 分鐘；**保留間隔 6 個月以上時，最佳 ISI 至少 1 個月**。
   - 擴張式間隔 (expanding ISI)：證據顯示**大致等同於**固定間隔，並未如流行說法般更優。
   - ⚠️ 論文明說間隔 vs 集中這一段「因效果量資料不足，只報告正確率差異」（271 組比較僅 23 個效果量），所以**這篇沒有給出一個統整的 spacing 效果量 *d***。
   - ⚠️ 廣為流傳的「最佳 ISI ≈ 保留間隔的 10–20%」**不在**這篇論文裡；那是 Cepeda 等人後續（2008）的研究，我本次**沒有**核對。
4. **對 AI 共學的設計含意**：排程不能只有一個固定的「明天再問你一次」。使用者的目標日期（期末考、面試、certification）應該是排程的輸入：目標愈遠，第一次複習間隔就該愈長。另外，「擴張式間隔更好」這個常被寫進 SRS 產品文案的說法，在這篇後設分析裡沒有支持。

### 8b. Dunlosky, Rawson, Marsh, Nathan & Willingham (2013) — 總覽與效用評級

1. **來源與作者年份**
   - Dunlosky, J., Rawson, K. A., Marsh, E. J., Nathan, M. J., & Willingham, D. T. (2013). *Improving students' learning with effective learning techniques: Promising directions from cognitive and educational psychology.* **Psychological Science in the Public Interest, 14**(1), 4–58.（卷期頁碼經 Crossref 核對） DOI: 10.1177/1529100612453266
   - 抓取並核對的全文 PDF：https://www.whz.de/fileadmin/lehre/hochschuldidaktik/docs/dunloskiimprovingstudentlearning.pdf ；期刊頁：https://journals.sagepub.com/doi/10.1177/1529100612453266
   - 標籤：【同儕審查】
2. **研究設計一句話**：系統性回顧，對 10 種學生可自行使用的學習技巧逐一評估其跨年齡、跨材料、跨測驗型態的證據強度，並給出「效用」評級。
3. **關鍵數字／原文評級（逐字抄自論文）**
   - **高效用 (high utility)：practice testing（練習測驗）、distributed practice（分散練習）** ——「received high utility assessments because they benefit learners of different ages and abilities and have been shown to boost students' performance across many criterion tasks and even in educational contexts」。
   - **中效用 (moderate utility)：elaborative interrogation、self-explanation、interleaved practice** ——降級理由是「the evidence for their efficacy is limited… elaborative interrogation and self-explanation have not been adequately evaluated in **educational contexts**」。
   - **低效用 (low utility)：summarization、highlighting、keyword mnemonic、imagery use for text learning、rereading**——論文直接建議「other techniques should be used in their place (e.g., **practice testing instead of rereading**)」。
   - 本文是敘述性回顧，**沒有**給出整合效果量。
4. **對 AI 共學的設計含意**：如果只能做兩件事，做**出題測驗**與**排程分散**。自我解釋雖被評為中效用，但降級理由是「缺少教室情境的驗證」而非效果小（對照 6c 的 *g*=.55），所以仍值得做，只是應該自行蒐集真實使用情境的數據。反過來說，摘要／畫重點／重讀這類「AI 幫你整理成筆記」的功能，正是被評為低效用的那一組。

---

## 9. Illusion of competence / fluency illusion（能力錯覺／流暢錯覺）

> **學習判斷 (JOL, judgment of learning)**：學完一項材料後，自己預測「之後測驗時我記不記得住」的評分。
> **能力錯覺**：JOL 系統性高於實際回憶率，因為學習當下看得到的線索（答案就在眼前）測驗時並不存在。

1. **來源與作者年份**
   - Koriat, A., & Bjork, R. A. (2005). *Illusions of competence in monitoring one's knowledge during study.* **Journal of Experimental Psychology: Learning, Memory, and Cognition, 31**(2), 187–194. DOI: 10.1037/0278-7393.31.2.187
   - 抓取並核對的全文 PDF（作者實驗室）：https://bjorklab.psych.ucla.edu/wp-content/uploads/sites/13/2016/07/Koriat_RBjork_2005.pdf ；PubMed：https://pubmed.ncbi.nlm.nih.gov/15755238/
   - 標籤：【同儕審查】
2. **研究設計一句話**：配對聯想學習作業，操弄詞對之間「先驗關聯」（看到線索詞時本來就會想到目標詞的機率）與「後驗關聯」（兩詞並列時感覺起來有多相關）的落差，比較 JOL 與實際回憶率。
3. **關鍵數字（抄自論文 Results 段）**
   - **實驗 2（順向 vs 反向聯想詞對）**：同一批詞對，順向排列時線索→目標關聯強度 .397，反向排列時僅 .020。
     - 順向：JOL **78.1** vs 實際回憶 **78.8**，t(19)=0.20（校準幾乎完美）
     - **反向：JOL 75.7 vs 實際回憶 60.3，t(19)=4.09, p<.001**（高估約 **15 個百分點**）
     - 關鍵不對稱：聯想方向對 JOL 幾乎沒影響（t(19)=1.16, ns），對實際回憶卻影響很大（t(19)=4.00, p<.001）。交互作用 F(1,19)=18.73, p<.0005。
   - 無關聯詞對也出現高估：JOL **37.32** vs 回憶 **24.28**，t(19)=3.48, p<.005。
   - **實驗 3（先驗 vs 後驗關聯）**：先驗關聯詞對 JOL 與回憶相符 t(15)=0.9, ns；**純後驗關聯詞對顯著高估** t(15)=3.79, p<.005；無關聯詞對亦高估 t(15)=3.77, p<.005。論文結論用語：「the purely a posteriori pairs produced a **marked illusion of knowing**」。
   - 論文亦引述既有發現：**延遲做的 JOL 比立即 JOL 準得多**，且此效應「只在以線索單獨提示（而非線索—目標並列）時出現」。
4. **對 AI 共學的設計含意**：這篇給出了機制層級的設計規則——**錯覺來自「判斷當下答案還在畫面上」**。所以 (a) 絕不要在答案仍顯示於畫面時詢問「你覺得你記住了嗎」；(b) 若要收集使用者的自評，應延遲、且只給線索不給答案（等同於一次提取練習）；(c) 更穩健的做法是根本不用自評當排程依據，改用實際提取表現。這條與第 2 項 Roediger & Karpicke 實驗 2 的信心反差互相印證：使用者的主觀把握是最不可靠的訊號，卻也是產品最容易誤用的訊號。

---

## 沒查到或沒確認的事

依重要性排序：

1. **Butterfield & Metcalfe (2001) 的正文與任何數字**——付費牆，只取得期刊官方摘要（無數字）。網路上廣傳的 gamma **G = .36** 我**無法**在該篇確認；我查到的 gamma 值（.63、.22、pr=.11）全部來自 2006 年那篇論文，而 2006 年重新分析的是作者標為 **2001b 的另一份研究**（"Updating the egregious"），不是 2001a 那篇 JEP:LMC 論文。**若要在產品文件引用數字，這一條必須先取得 2001 年原文。**
2. **Fiorella & Mayer (2013) 的任何一手數字**——全文與摘要皆付費牆。設計與結論方向是從作者本人 2023 年回顧章節轉述的【二手】。該章節提到的 Kobayashi (2019) 後設分析 *d*=.30–.40 我也**沒有**核對原文。
3. **Nestojko et al. (2014) 的效果量與百分比**——只取得期刊官方摘要（無數字），全文 PDF 鏡像回 403。
4. **Chi et al. (1989) 的正文**——Wiley 與 ScienceDirect 皆 403。「15.3 vs 2.8 個解釋／82% vs 46%」這四個數字是 Chi 本人在 1994 年論文中的自述，屬【二手】轉述。
5. **Slamecka & Graf (1978) 實驗 1 的分項均值**——論文正文未列，只畫在 Figure 1、Figure 2；我沒有目測圖上的數值。目前可引的只有 F 值，以及實驗 5 的 .68 vs .61。
6. **Cepeda et al. (2006) 沒有一個統整的 spacing 效果量 *d***——論文自己說效果量資料不足（271 組比較僅 23 個效果量）。另外流行的「最佳間隔 ≈ 保留間隔的 10–20%」**不在這篇**，應是 Cepeda et al. (2008)，本次未核對。
7. **Bjork Lab 官網沒有 publications 索引頁**——https://bjorklab.psych.ucla.edu/ 首頁導覽列只有 About / Research / People / Useful Links & Media / Videos / Gallery / Contact，沒有出版品清單，也沒有 desirable difficulties 的定義段落；本檔引用的兩份 PDF 是直接掛在該站 wp-content 目錄下的檔案。/publications/ 路徑回 404。
8. **Feynman 技巧的一手出處：確定沒查到。** 搜尋不到任何 Feynman 本人提出該四步驟方法的文獻。「最早出現於 2011 年」這個說法本身也是部落格互相轉引、無可查證檔案，我**不**把它當成已確認事實。
9. **Bjork & Bjork (2011) 章節的編者與出版社**——該章節無 DOI，Crossref 查不到；PDF 頁眉只顯示書名 *Psychology and the Real World*、第 5 章與 pp. 55–64，編者與出版社我未核對，正文引用中已略去。
10. **Bjork (1994) 章節中的西班牙文字彙實驗**——章節內未給原始出處的完整書目（文中未標 Bahrick 年份於該段），我未追到該原始研究。
