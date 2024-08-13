---
title: ( Writeup ) AIS3 Pre-Exam & MyFirstCTF 2024
published: 2024-08-08
description: 'AIS3 Pre-Exam & MyFirstCTF 2024'
image: ''
tags: [Writeup, CTF]
category: 'WriteUps'
draft: false 
---

# AIS3 Pre-Exam 2024 WriteUp

:::tip[原始附圖 HackMD 連結]
https://hackmd.io/@xiung07/SyyNnaCXA
:::

## Welcome

題目上的敘述即為答案。

**FLAG**： `AIS3{Welc0me_to_AIS3_PreExam_2o24!}`

---

## Quantum Nim Heist

**STEP 1.** ： 輸入 `0` 開始遊戲，先進行第一輪遊戲，分別輸入 `0`、`0`、`1`。

**STEP 2.** ： 重複輸入 `3` 跳脫遊戲預設的操作方法，直到只剩下一個石頭。

**STEP 3.** ： 分別輸入 `0`、`0`、`0`，移除最後一顆石頭，贏得遊戲，取得 FLAG。

> **原理** ： 根據原始碼 `server.py` 可知，程式並沒有判斷 **0 ~ 2** 以外的選項，故執行 **3** 則不會執行任何的動作，整場遊戲會由程式獨自進行，它的機制則是取得勝利，故遊戲最終都只會剩下一顆石頭。

**FLAG**： `AIS3{Ar3_y0u_a_N1m_ma57er_0r_a_Crypt0_ma57er?}`

---

## Three Dimensional Secret

> 根據題目的名稱可知，此題與三維空間有關。

**STEP 1.** ： 使用封包查看工具 Wireshark 開啟檔案，由上而下觀察輸出字元量，可以發現第 75 項封包是第 1 個出現最多字元的封包。

**STEP 2.** ： 將 Hex 資料轉碼後，可以得到一串 GCode 程式碼的起始。
```
;FLAVOR:Marlin
;TIME:788
;Filament used: 8.45726m
;Layer height: 10
;MINX:58.496
;MINY:58.537
;MINZ:10
;MAXX:241.742
;MAXY:241.259
;MAXZ:10
;TARGET_MACHINE.NAME:Creality Ender-3 Max
;Generated with Cura_SteamEngine 5.6.0
M140 S60
M105
M190 S60
M104 S200
M105
M109 S200
M82 ;absolute extrusion mode
; Ender 3 Max Custom Start G-code
G92 E0 ; Reset Extruder
G28 ; Home all axes
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X0.1 Y20 Z0.3 F5000.0 ; Move to start position
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; Move to side a little
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line
G92 E0 ; Reset Extruder
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X5 Y20 Z0.3 F5000.0 ; Move over to prevent blob squish
G92 E0
G92 E0
G1 F1500 E-6.5
;LAYER_COUNT:1
;LAYER:0
M107
G0 F6000 X112.332 Y147.783 Z10
;TYPE:SKIRT
G1 F1500 E0
G1 F1200 X113.079 Y147.223 E1.55258
G1 X113.47 Y146.956 E2.33996
G1 X114.19 Y146.505 E3.75283
G1 X116.867 Y144.974 E8.88134
G1 X118.944 Y143.698 E12.93516
G1 X121.09 Y142.298 E17.19626
G1 X123.156 Y140.864 E21.37855
G1 X124.111 Y140.161 E23.35062
G1 X124.258 Y140.063 E23.64443
G1 X125.264 Y139.326 E25.71833
G1 X125.734 Y139.001 E26.66861
G1 X126.281 Y138.644 E27.75487
G1 X128.402 Y137.222 E32.00148
G1 X130.461 Y135.755 E36.20582
G1 X132.479 Y134.23 E40.41225
G1 X134.428 Y132.667 E44.56696
G1 X136.364 Y131.023 E48.79075
...
```

**STEP 3.** ： 依序將所有在封包中發現 GCode 程式碼整理成一個檔案，並用線上 GCode 檢視工具開啟程式。最後得到 FLAG。

**FLAG** ： `AIS3{b4d1y_tun3d_PriN73r}`

---

## Evil Calculator

**STEP 1.** ： 透過開發者工具，檢視操作 `1 + 1 = ` 時的網路請求，可以發現 POST 請求中有一項 `expression` 的資料。

**STEP 2.** ： 查看原始碼 `app.py` 中第 11 行，`result = eval(expression)`，使用了 eval() 的函式，可以用於執行 Python 程式字串。同時已知 FLAG 檔案位於主程式上上層資料夾。

**STEP 3.** ： 回到開發者工具，將 `expression` 的內容改為 `open('../../flag', 'r').read()`，並重新傳送，即獲得 FLAG。

**FLAG** ：`AIS3{7RiANG13_5NAK3_I5_50_3Vi1}`

---

## The Long Print

**STEP 1.** ： 根據題目敘述，使用 Ghidra 或其他反編譯工具開啟檔案後，從 `main()` 的函式底下，可以得知以下流程

> 輸出一句話 -> 進入迴圈 -> `sleep(0x3674)` -> 輸出一個字元

**STEP 2.** ： 從 `sleep(0x3674)` 16 進位換算後，可知程式會在等待 13940 秒後輸出一個字元。藉由反編譯工具得知 `0x3674` 位於 `122b` 的位置，使用 Hex 編輯器修改為 `BF 01 00 00 00` 並另存名為 `flag-printer` 的檔案。

**STEP 3.** ： 在 x86 架構的 Linux 系統中，先執行 `chmod +x flag-printer` 給予檔案權限，再使用 `./flag-printer` 執行檔案。

程式會以每秒一個字元的速度輸出 FLAG，並在最後消失。

**FLAG** ： `AIS3{You_are_the_master_of_time_management!!!!?`

---

## babyRSA

> ### RSA Info
> 公鑰 = {e, n}
> 私鑰 = {d, n}
> 明文 = m
> 密文 = c
>
> 加密方法： m ^ e mod n = c
> 解密方法： c ^ d mod n = m

**STEP 1.** ： 由 `output.txt` 中，可知公鑰 `{e, n}` 和密文 `c` 各個字元的陣列，若需要解密則需要私鑰 `d`，故我寫了爆破解密的程式碼。

```python=
e,n = 64917055846592305247490566318353366999709874684278480849508851204751189365198819392860386504785643859122396657301225094708026391204100352682992979425763157452255909781003406602228716107905797084217189131716198785709124050278116966890968003294485934472496151582084561439957513571043497031319413889856520421733, 115676743153063753482251273007095369919613374531038288437295760314264647231038870203981488393720761532040569270340726478402172283300622527884543078194060647393394510524980830171230330673500741683492143805583694395504141751460090539868114454005046898551218623342425465650881666420408703144859108346202894384649
c = [59582983136368434856816799733313446746433796034384724221174424464969737874802116129348607979328098841766335449896610931770063087921739964156335144291643702667891887833963756948394265219864837961748202920096128332905237576243643095664147826020400199347355043162641743846198725931842313977049712473768688780204, 95359547394031742813518330673269556403528254059894407470006786975603938062435320319282644182444182438612748874603359501010449113346386193598111715879103479311697744375488228536365895249959983701008182395138745363343749821348881488616739650767615867269542213617639437927373484681942750228038458670913761461906, 46329325300279098651694178842591774415260876326218182283454895682597312145324055490326488805186682301528705330448500034219715636964856131530973835780285303243952273742119154142469279746360304190118988650200422700136950019141246372634642054318988506247030406078971388938494583721698317950574261574174233878465, 99372516099607712778908802720080113062724120782160998443385643772511391370661101893707293382044546993124605549696368316348952556779713164710839853078160450782104255053788389238478472574549113909833434906535103012424826026640284958298083646000213492094244631381094489147645893989473799375006911204994971262513, 76560888147807476608165550435978536197327212318831455594999273843368454289391559274947371380742007729563677938535717707232627424457601159959128489070947748904688640279908482263289424669338790488996485849079890881530740377280113682547250364463080771156212510360194563192123664613212111565777733487081937952558, 12319813533472769541026063795801870849236715810997656653501875874806446093919930377755747066386074676697058702735112064576219731845584108035461434499628574742399407498867908576045220515065246483998134315307132901329833371485817530138131352593805641664023978795298886913968639954517583992930243922021434381738, 96951009388162450018398074248238612521098089563081241061172635732154749686698900516806076917644927142046116130006730586770841058020946718314769404592479949673385387831784647829787593435525861689652400487918043078535385527278516028607916478700007746817161408140805937414915909575928550204945457887011906141614, 1665805297521640119669919457094144711238099413231800824465470812913880572669116305626521524153911904267238129531937952423409222225023467794927666422627082314285814656075569814644205638687105792760533211008966815918943917251927254624389871965679206262024216136163262412286874416732008465838711695063592124435, 38031617734525236754862788270684927634041250565347090806313746312968815507316236887544784308484734926981400017478758364119367924220519253824976349577671434162884831759762106665665138444165001645856871491952279748415831766579735400499998753646766301606966507940299051979075677572064596983713461662607114250263, 64855027109789931203406858899259092299626327163376469398846102754805420506427252072315662287801006608894162707199492268892939811482863649987034183371607590158980649349849687594118554925649076468007225363531072941142253057862686631842080812159597499430107963982315266135241847383726503265496996481889717246182, 38285633551521777138710771085033430239170710707266775875598456653892976969437761922968925746226073683095654278272586779539831402373205526909772633370025848937463033570747721110932401276480992827694564074802181306738438015295515798739406061377284368603543443076476369810597345436481251791260803352288977423573, 90968383857681404242927909477464252602387471219945950453665598772039832078487309149670692874283215437984574695320981806360379096212936326954111131608499584545969103096096842380877613033764006459107764277330135816044808210474597200172673683909558627722941503504083244424751773797618360290613996970960151563724, 95457298055868694391877219138576497445115151186056513418820503159496876268497080831408725541436969299827723238663173668798694515208450035233192338795425824459299174728295661096981248839235055855929604893139239340445385259232905864515397406993189217322907168121716905101208450279521267289056195400878302077398, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 34661612116812273325510815885632987773878634626625747042958636362152583931260969561869719786378247664638641161656878412129162010084766438156247296031040184022246208883138926132649114007757242227131459285251878118564710945280013332131793855359773876332415772442620349609897435915019325055421286197078708187352, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 67602482196856820166971428403758405739455475263382367621161896414339370625380754447863410276767241406699969322350803814348655243066328706656427717483623041308690996376549835317954286006923639192767262817817435759143930376297271756237829141630002480289781731985465743331200468015517012225741723423203374827341, 44262354102194743351911268289256770008339497245528544280709170541530088518398751380655719846628700171065092804544687718896291531970838072744874705570156704628202662829757806782131182294252555844059856971743311355891113953747318316062265029166813195656690004051327523203179399349334322871113218009692321746302, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 17525442355443739006798161136945234538289135293732159010469949341666347513585837371870704355037863634098163883611042121878362686860890223724238562583526550649340086051319234134907577624853632886715848962127706255769976443912657070070366400669740596805962173530384420842637554803041466900119050709458062167550, 44014841046589017601891983719958867760419600204352901036629548332837496204051161377933425345536644034794916246706885620488552830053604204333015893225255398323167834260921720336325397193593333461140140475610284003097590899522403244883330800589948361851693870192559674072749868797979125684663605722325053340834, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 32463563387229396502994321924065961632284043136468238906625180045736135155253223928723914405824284085442712712337348213915399745045346853697650399659292339726614512642835897683094877670342609027803404027945312939777902664125228095034970967750466928999176126534504349068649205422848461193336320361026425455874, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 12179738687529782107447590339149361896936551322934825418520525165858885435598866363152322677187041910693780230742925050834968940508546403788452893248148672885195158450001568668626998159030042932978014971079411358732702590558133373478956077176801426446288446920254354063720982962966912703174841575095158773376, 101125682339799901828662987568918086070282069568379908090074247169217184659644669626554838396604623590909101664987452894437649857681299514293609000818253780343589956828098266778252516930801354335366245918603834198786544373944956666900784678369416240212915856834107510529441463083826031881209805666209335413628, 101403644290884991310189664359755656780537902543354415482434580937410695343294757120985680350019917171639284125327989098680673553323894980248499865788837636944758311200094760909373728675822272584823764964753326309765279310435693879623302965536053211433064599526550676915084290753201910772032395483945950367273, 24333051506853181360030701569319128673885779416125109480872653360245763695810807795571148802002658160356587851857338891650119080260776136984074861612952869696123011417276568821410663401888348228549042676235853145756762295087473309782699704381451505573652641489540319626561348999020895690560418530256831740666, 20222920908058605457111970272150612273139460769260447235596498596781683961010128426184024637706564546340327246191020540223566835757304493325371606037680402571948650998523099138137441154209281794538860160477031997660506452095283151142470607354579609545040759974018408429796935802188551530478970289514572978617, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 32184464000490155748453165982143565340499464338829080683417468389784993809512708479494827939476307049612151190695993375700147700844413744001417893095868641387694266647992101758785355055413538046252854525860440227182911367045556141460084455472907278113962890024281663648508886642376786194323597791020547317088, 101125682339799901828662987568918086070282069568379908090074247169217184659644669626554838396604623590909101664987452894437649857681299514293609000818253780343589956828098266778252516930801354335366245918603834198786544373944956666900784678369416240212915856834107510529441463083826031881209805666209335413628, 44014841046589017601891983719958867760419600204352901036629548332837496204051161377933425345536644034794916246706885620488552830053604204333015893225255398323167834260921720336325397193593333461140140475610284003097590899522403244883330800589948361851693870192559674072749868797979125684663605722325053340834, 12179738687529782107447590339149361896936551322934825418520525165858885435598866363152322677187041910693780230742925050834968940508546403788452893248148672885195158450001568668626998159030042932978014971079411358732702590558133373478956077176801426446288446920254354063720982962966912703174841575095158773376, 63634815088527144255090148113948593793648445499224983027630191877159813968754095341812467946868079279626991968747689424489021633678743106301884613005477044402324870044751927862596590687251830485165119422247449722579599610918927243419033509419967393677988976255284611384351411782311379786356079256916831362626, 9304987377904341606117201715658113065608581640101320211543462955469900806281721467187032121463132314663326494170970278379001634044806680348131292368949519512445580695938064509920503814133961673755470696223243390646274004621955993274826096679460577701554059204349111764901921932386091658007427259226167178177, 58828925452729811932976588739787965824652220690551333824296205824127538696058603108169405357158211350616510470513672533759883740745736322687898383422522330915631984810878357007178714597068087752425823728826608887027664209314455243118645386520598961325656254330576959063500755210398248129074822706590225088700, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 32184464000490155748453165982143565340499464338829080683417468389784993809512708479494827939476307049612151190695993375700147700844413744001417893095868641387694266647992101758785355055413538046252854525860440227182911367045556141460084455472907278113962890024281663648508886642376786194323597791020547317088, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 12179738687529782107447590339149361896936551322934825418520525165858885435598866363152322677187041910693780230742925050834968940508546403788452893248148672885195158450001568668626998159030042932978014971079411358732702590558133373478956077176801426446288446920254354063720982962966912703174841575095158773376, 38031617734525236754862788270684927634041250565347090806313746312968815507316236887544784308484734926981400017478758364119367924220519253824976349577671434162884831759762106665665138444165001645856871491952279748415831766579735400499998753646766301606966507940299051979075677572064596983713461662607114250263, 42674155454878392842592499423860033988264245394501952163129442865919203299671995689679354090226093903765768139477289952989042795959374257614752953563152551974557414325407858919156902405925850703390450181868760242922958259454422450849566085988801215229822701373233313619020572460459663094142218119144686335871, 101125682339799901828662987568918086070282069568379908090074247169217184659644669626554838396604623590909101664987452894437649857681299514293609000818253780343589956828098266778252516930801354335366245918603834198786544373944956666900784678369416240212915856834107510529441463083826031881209805666209335413628, 75918055185950026238164530295762591705002247585433355113315303142207464051952569831664550604622541858093495062851840811257603174544255151597115446984458237694842739071530518936317606199598953518976167711716762043806043449827887577909963803673508838826582484003687958862302989732473748700329398645880243054148, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 42674155454878392842592499423860033988264245394501952163129442865919203299671995689679354090226093903765768139477289952989042795959374257614752953563152551974557414325407858919156902405925850703390450181868760242922958259454422450849566085988801215229822701373233313619020572460459663094142218119144686335871, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 47953002091054578020381201294163023730809574731463958773592358719441988187452655748118051277286650853337305718192021972814357369747332979634917684999259838316305009239963225119133204824897098152777119627043881500966537112886938182847527574241215915396651397126350467492479189194162628876529519538265140143596, 101403644290884991310189664359755656780537902543354415482434580937410695343294757120985680350019917171639284125327989098680673553323894980248499865788837636944758311200094760909373728675822272584823764964753326309765279310435693879623302965536053211433064599526550676915084290753201910772032395483945950367273, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 107340541989905757204370662416845552037146078905222935505789033122562501577684655501092154588544305605078885306044047839464564901594898750722560559313996616820973286189602827331851868376927628179028545097753144658073207307785378721899783095713473789007231709234504050418717400729711972350669632384570468096830, 43967923748936484351732805873555964174712775706889811180819474140612599586161884530658035908721232399384729457223641995556425707839305124083600738135036620220298476686325110132022730675370888898063942501477522619906479683016701151321856269078215479158146009655223314957908787521092587379267241203076718674092, 24333051506853181360030701569319128673885779416125109480872653360245763695810807795571148802002658160356587851857338891650119080260776136984074861612952869696123011417276568821410663401888348228549042676235853145756762295087473309782699704381451505573652641489540319626561348999020895690560418530256831740666, 20472445493228441292721090614657967895462252302228260568752427996680563809601852655319833688134475798137834395223726607334321531235376774219216055134601030184130917876549113091114144486261794932716233808194664233936783735663266743029212488020840969559603523111887524998658108503660068448898570323437482810017, 72893301186321303683272295658327353212060838237559048917336264819112421968115615005989580760612444279776561566669272155758039717810976344470895667733780292960024364644216982543515945404550612735708418065359731237914621596888496631385879381090937225999965270114589266587955012094766794851372212812150698234716, 64855027109789931203406858899259092299626327163376469398846102754805420506427252072315662287801006608894162707199492268892939811482863649987034183371607590158980649349849687594118554925649076468007225363531072941142253057862686631842080812159597499430107963982315266135241847383726503265496996481889717246182, 52468135911274945777529136529541932989316502665934748207836694395110108517204287366878248216053327656034128346107236076714109214143042824050810182510919475258788845504651287598248217763885663385525333584236650693667648746329707103824387098563158188013686337454772275459145419197015884603853408230553227896407, 75918055185950026238164530295762591705002247585433355113315303142207464051952569831664550604622541858093495062851840811257603174544255151597115446984458237694842739071530518936317606199598953518976167711716762043806043449827887577909963803673508838826582484003687958862302989732473748700329398645880243054148, 12708160939460449797746334640370189741594393156198590130563300705594742285274155378452384449752817944962371880018673966875751948953034846634284138305820292201281595210265881917297911731564408181887226462606892964361033320116765426523499831287478314065882300932476595216136350756971622192468975464823677154324, 52745488365658861485519010696623986434656675831322149607647058389953842185045922621964255927212518970223978973817292179059730382537814695353016058702226289640834171560498112170760826276332972100423555174686162215383841925656596984188536350046664199627214379076416024495451320834231863438007383528385204646269, 8855798603366167912634233401398286651752671525801140525178611090639905433230380535711326462952071294452556819384200831430822862220907470038589552641363759764881881084119960616686113091264272665290715332905431138686504873774368450566561688814993821800992967990682116846800657243011069696481920893909247794983, 96951009388162450018398074248238612521098089563081241061172635732154749686698900516806076917644927142046116130006730586770841058020946718314769404592479949673385387831784647829787593435525861689652400487918043078535385527278516028607916478700007746817161408140805937414915909575928550204945457887011906141614, 3085377115073481737487767519304315808353144937670566256348398664810936964565637157736537945459712875615504238408907602974507381828272609303797146395233485026377776965939508974096385939172942695211339651597248692728550782246178293579153110379844451779466255357619524290412118137515779354431956948078394927940, 48345447683174081443502925378502329908064423944850311779861406407783604557812792515281621715817536127803162311234459315836524837064977025182379655213338205159741266326939713833052921255157742860610743189155260503439836583887313584730345974553768985184119012533854386867355018502198395672167297716386558437643, 2943509185067047938273565758747957807917637430462018374124947856251091022696853505230975399503014099411245162812979057344198094444949853114144790397928000334361276864689352349519363636219566973775714458213611238774130167222835759501223813455853370320854862131109567941072112035263351158877256955712543549605, 76560888147807476608165550435978536197327212318831455594999273843368454289391559274947371380742007729563677938535717707232627424457601159959128489070947748904688640279908482263289424669338790488996485849079890881530740377280113682547250364463080771156212510360194563192123664613212111565777733487081937952558]

output = ""
for item in c:
    for character in range(0,127):
        if pow(character,e,n) == item:
            output += chr(character)

print(output)

# output = @)!,*^=AIS3{NeverUseTheCryptographyLibraryImplementedYourSelf}-=1#&*
```

> **程式原理**：從 ASCII Code 中共 128 個字元根據加密方式 `m ^ e mod n = c` 匹配是否與加密後的字元相同，若相同則將 ASCII Code 轉換為一般字元並儲存。

**FLAG** ： `AIS3{NeverUseTheCryptographyLibraryImplementedYourSelf}`