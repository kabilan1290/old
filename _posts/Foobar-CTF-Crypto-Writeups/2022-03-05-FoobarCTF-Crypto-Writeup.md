---
layout: post
title:  "ZoobarCTF 2022 Crypto writeup"
date:   2022-03-05 09:29:20 +0700
categories: bugbounty
---


<h2 id="crypto-challenge-writeup-">Crypto Challenge Writeup:</h2>
<h4 id="challenge-name-babyrsa">Challenge name : babyRSA</h4>
<p>Chall file:</p>
<pre><code>from Crypto.Util.number <span class="hljs-built_in">import</span> *

<span class="hljs-attr">flag</span> = b<span class="hljs-string">"GLUG{**********REDACTED***************}"</span>

p,<span class="hljs-attr">q</span> = getPrime(<span class="hljs-number">1024</span>),getPrime(<span class="hljs-number">1024</span>)
<span class="hljs-attr">N</span> = p * p * q
<span class="hljs-attr">e</span> = <span class="hljs-number">0</span>x10001
<span class="hljs-attr">phi</span> = p * (p-<span class="hljs-number">1</span>) * (q-<span class="hljs-number">1</span>)

<span class="hljs-attr">d</span> = inverse(e, phi)
<span class="hljs-attr">m</span> = bytes_to_long(flag)
<span class="hljs-attr">c</span> = pow(m, e, N)
<span class="hljs-attr">x</span> = (p * q) % <span class="hljs-number">2</span>**<span class="hljs-number">1337</span>

print(<span class="hljs-string">"N =  {}"</span>.format(N))
print(<span class="hljs-string">"e =  {}"</span>.format(e))
print(<span class="hljs-string">"c = {}"</span>.format(c))
print(<span class="hljs-string">"x =  {}"</span>.format(x))
</code></pre><p>Output:</p>
<pre><code><span class="hljs-attr">n</span> =  <span class="hljs-number">1862871955255773903015555578091085043612025268407412897302905832620896712025201897312995829553474175135190551094563297876709351981276817349813398258043249300488609960386863268339518629692154323824453110186146306010963662305861966120667538777155455159739296406335349293672176307554123622232777012514054848468930806904131960465772223360786428469291726206998215694107790773315484457273571590016126063412114288248673526367649685670511041861887410170073444517108820725764459594983621584194625078860139865684193327199600381598274487395551130322199129914933955585468166778481081659128795093350101559570647565453632652791437814144529373413332850183156364883440821142181920071845112312902857853441611155344240891225716213626273473705542052460314619334514865005147194913072907414167355710711865745558531586223316679842899089181138483727879743039536045989818393934040285179823266327242407657963285329559029697302427490888420166241313849</span>
<span class="hljs-attr">e</span> =  <span class="hljs-number">65537</span>
<span class="hljs-attr">c</span> =  <span class="hljs-number">1685694402585229399699468599208783295407454759540674091938668979180243955130403003626211101984152370742736211045525984316632749971433168883287383299645245330127391420455774558657472055645730820864774304037508627351147925419052692787660808285791081689100274893400562022977620012146667522246835047767037511180880573701661280754732681902586578745711467339809022597202965059918776095504683478094646033528621093492050170604923556981705249683385011326853931024569288136526731192525998067880512627462749891475398515167819479784915263417194960798808797837840025469943383251121317715074631999894894231902525637411310950615560785726619602252227413380519591407687610748182270093352374680299466331526526996398038485430377656603679759082275850216111391654800091554055155641275315162407470247790599682283615245968236163550075270783003654577185666636437073672252763233925941850862748625937949315172143452560017994833819344332542074129579377</span>
<span class="hljs-attr">x</span> =  <span class="hljs-number">2121556837489323393830117506636485275262970334096973330971400998541061434248491797293936443932014206079161678574395103482971809478068410664024927982076981551873713173206728190862242836376526528390743086543538755826674529105687167529814363908630371800401390496769993532681737968141810119668479989445768189718574035122948285385181466041802061542422715767093719363420978170488364616302423479875250423116981</span>
</code></pre><ul>
<li>This is an normal RSA challenge with extra value x = <code>p*q % 2**1337</code>.</li>
<li>Here n is of <code>p*p*q</code>and my solution lies on the below equations.</li>

<img src="https://raw.githubusercontent.com/kabilan1290/crypto/main/challenge/foobarCTF/image0.jpg"><img>
<br></ul>
<p>- once we retrieved p,we can easily decrypt the flag.</p>

<h4 id="script-">Script:</h4>
<pre><code>from gmpy <span class="hljs-built_in">import</span> *
from Crypto.Util.number <span class="hljs-built_in">import</span> *
<span class="hljs-attr">n</span> =  <span class="hljs-number">1862871955255773903015555578091085043612025268407412897302905832620896712025201897312995829553474175135190551094563297876709351981276817349813398258043249300488609960386863268339518629692154323824453110186146306010963662305861966120667538777155455159739296406335349293672176307554123622232777012514054848468930806904131960465772223360786428469291726206998215694107790773315484457273571590016126063412114288248673526367649685670511041861887410170073444517108820725764459594983621584194625078860139865684193327199600381598274487395551130322199129914933955585468166778481081659128795093350101559570647565453632652791437814144529373413332850183156364883440821142181920071845112312902857853441611155344240891225716213626273473705542052460314619334514865005147194913072907414167355710711865745558531586223316679842899089181138483727879743039536045989818393934040285179823266327242407657963285329559029697302427490888420166241313849</span>
<span class="hljs-attr">e</span> =  <span class="hljs-number">65537</span>
<span class="hljs-attr">c</span> =  <span class="hljs-number">1685694402585229399699468599208783295407454759540674091938668979180243955130403003626211101984152370742736211045525984316632749971433168883287383299645245330127391420455774558657472055645730820864774304037508627351147925419052692787660808285791081689100274893400562022977620012146667522246835047767037511180880573701661280754732681902586578745711467339809022597202965059918776095504683478094646033528621093492050170604923556981705249683385011326853931024569288136526731192525998067880512627462749891475398515167819479784915263417194960798808797837840025469943383251121317715074631999894894231902525637411310950615560785726619602252227413380519591407687610748182270093352374680299466331526526996398038485430377656603679759082275850216111391654800091554055155641275315162407470247790599682283615245968236163550075270783003654577185666636437073672252763233925941850862748625937949315172143452560017994833819344332542074129579377</span>
<span class="hljs-attr">x</span> =  <span class="hljs-number">2121556837489323393830117506636485275262970334096973330971400998541061434248491797293936443932014206079161678574395103482971809478068410664024927982076981551873713173206728190862242836376526528390743086543538755826674529105687167529814363908630371800401390496769993532681737968141810119668479989445768189718574035122948285385181466041802061542422715767093719363420978170488364616302423479875250423116981</span>


<span class="hljs-attr">N</span> = <span class="hljs-number">2</span>**<span class="hljs-number">1337</span>
<span class="hljs-comment">#x = (p * q) % 2**1337</span>

<span class="hljs-attr">d</span> = inverse(x,<span class="hljs-number">2</span>**<span class="hljs-number">1337</span>)

<span class="hljs-attr">p</span> = d * n % N 

<span class="hljs-comment">#print(p)</span>

<span class="hljs-attr">p</span> = <span class="hljs-number">139387518986826536509144049219128127758120617287987945818827279314602564142349682985769656023092456357155296439247793458178075113354440414475867450674127064081966333660528171321853175654236647699883371985059454473707508426015442275267745798448560385920139928240405884255349873045306634707812262346541658507253</span>

<span class="hljs-attr">p_p</span> = p*p

<span class="hljs-attr">q</span> = n // p_p

<span class="hljs-attr">phi</span> = p * (p-<span class="hljs-number">1</span>)*(q-<span class="hljs-number">1</span>)

<span class="hljs-attr">d</span> = inverse(e,phi)

print(long_to_bytes(pow(c,d,n)))
</code></pre>
<br>
<p>Flag : GLUG{ded1cati0n_i5_4_tal3n7_4ll_0n_it5_0wn}</p>
<hr>
<h4 id="challenge-name-daredevil-s-server">Challenge name :  Daredevil&#39;s server</h4>
<h4 id="description-">Description:</h4>
<p>Matt-Murdock is one my favourite character from marvel universe.</p>
<p>I found his msg signing server can you help me to retrive his secret.</p>
<ul>
<li>We were given with an netcat connection <code>nc chall.nitdgplug.org 30093</code></li>
<li>Connecting to the server gave us the below options.</li>

<img src="https://raw.githubusercontent.com/kabilan1290/crypto/main/challenge/foobarCTF/Screenshot_2022-03-05_19-50-10.png">
<li>I went on with the value E to know the encryption function!</li>
</ul>
<h4 id="output-of-encryption-function-">Output of encryption function:</h4>
<pre><code>TOKEN = <span class="hljs-string">b'd4r3d3v!l'</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">chall</span><span class="hljs-params">()</span>:</span>
    s = Sign()
    <span class="hljs-keyword">while</span> <span class="hljs-keyword">True</span>:
        choice = input(<span class="hljs-string">"&gt; "</span>).rstrip()
        <span class="hljs-keyword">if</span> choice == <span class="hljs-string">'P'</span>:
            print(<span class="hljs-string">"\nN : {}"</span>.format(hex(s.n)))
            print(<span class="hljs-string">"\ne : {}"</span>.format(hex(s.e)))
        <span class="hljs-keyword">elif</span> choice == <span class="hljs-string">'S'</span>:
            <span class="hljs-keyword">try</span>:
                msg = bytes.fromhex(input(<span class="hljs-string">'msg to sign : '</span>))
                <span class="hljs-keyword">if</span> TOKEN <span class="hljs-keyword">in</span> msg:
                    print(<span class="hljs-string">'[!] NOT ALLOWED'</span>)
                <span class="hljs-keyword">else</span>:
                    m = bytes_to_long(msg)
                    print(<span class="hljs-string">"\nsignature : {}"</span>.format(hex(s.sign(m))))      <span class="hljs-comment">#pow(msg,d,n)</span>
                    print(<span class="hljs-string">'\n'</span>)
            <span class="hljs-keyword">except</span>:
                print(<span class="hljs-string">'\n[!] ERROR (invalid input)'</span>)

        <span class="hljs-keyword">elif</span> choice == <span class="hljs-string">'V'</span>:
            <span class="hljs-keyword">try</span>:

                msg = bytes.fromhex(input(<span class="hljs-string">"msg : "</span>))
                m = bytes_to_long(msg)
                signature = int(input(<span class="hljs-string">"signature : "</span>),<span class="hljs-number">16</span>)
                <span class="hljs-keyword">if</span> m &lt; <span class="hljs-number">0</span> <span class="hljs-keyword">or</span> m &gt; s.n:
                    print(<span class="hljs-string">'[!] ERROR'</span>)

                <span class="hljs-keyword">if</span> s.verify(m, signature):                           <span class="hljs-comment">#pow(sign, e, n) == msg</span>
                    <span class="hljs-keyword">if</span> long_to_bytes(m) == TOKEN:
                        print(SECRET)

                    <span class="hljs-keyword">else</span>:
                        print(<span class="hljs-string">'\n[+] Valid signature'</span>)

                <span class="hljs-keyword">else</span>:
                    print(<span class="hljs-string">'\n[!]Invalid signature'</span>)

            <span class="hljs-keyword">except</span>:
                print(<span class="hljs-string">'\n[!] ERROR(invalid input)'</span>)


        <span class="hljs-keyword">elif</span> choice == <span class="hljs-string">'Q'</span>:
            print(<span class="hljs-string">'OK BYE :)'</span>)
            exit(<span class="hljs-number">0</span>)
        <span class="hljs-keyword">else</span>:
            print(<span class="hljs-string">'\n[*] SEE OPTIONS'</span>)
</code></pre><ul>
<li>Ok this is an classic RSA blinding attack,</li>
<li>You can learn more about this here <a href="https://masterpessimistaa.wordpress.com/2017/07/10/blinding-attack-on-rsa-digital-signatures/">https://masterpessimistaa.wordpress.com/2017/07/10/blinding-attack-on-rsa-digital-signatures/</a></li>
<li>We have to sign the message with the value <code>d4r3d3v!l</code>,where we not directly allowed to sign it.</li>
<li>First thing i did was to convert the <code>d4r3d3v!l</code> value to long.</li>
</ul>
<pre><code>     <span class="hljs-meta">&gt;&gt;&gt; </span>bytes_to_long(<span class="hljs-string">"d4r3d3v!l"</span>)
     <span class="hljs-number">1848453546913725555052</span>L
</code></pre><ul>
<li>Hence the output is composite,i went on with factorization and signing. (You can also solve this with blinding factor)</li>
</ul>
<pre><code>      {<span class="hljs-number">2</span>: <span class="hljs-number">2</span>, <span class="hljs-number">175143733</span>: <span class="hljs-number">1</span>, <span class="hljs-number">2638480856911</span>: <span class="hljs-number">1</span>}
</code></pre><ul>
<li>The above is the output for factorizing the TOKEN value in long.</li>
<li>The solution goes on by signing each individual integers and assign their signature in a,b,c,d.</li>
</ul>
<pre><code>       s<span class="hljs-comment">(m)</span> = a*b*c*d <span class="hljs-meta">%</span> <span class="hljs-symbol">n</span>
</code></pre><ul>
<li>we can get the value of n by sending the key P to the server which gives us publickey (n,e).</li>
<li>Once we calculated the signature,we can send hex value of m and s(m) to retrieve the flag.</li>
</ul>
<h4 id="solution-">Solution:</h4>
<pre><code>from sympy import factorint
from Crypto.Util.number import *
TOKEN = <span class="hljs-string">'d4r3d3v!l'</span>


<span class="hljs-comment">#1848453546913725555052</span>
<span class="hljs-comment"># composite number</span>
t = str(1848453546913725555052)
<span class="hljs-comment">#factorization</span>

<span class="hljs-comment"># {2: 2, 175143733: 1, 2638480856911: 1}</span>


<span class="hljs-built_in">print</span>(long_to_bytes(2).encode(<span class="hljs-string">"hex"</span>))

<span class="hljs-comment"># send 2 times</span>

<span class="hljs-built_in">print</span>(long_to_bytes(175143733).encode(<span class="hljs-string">"hex"</span>))

<span class="hljs-built_in">print</span>(long_to_bytes(2638480856911).encode(<span class="hljs-string">"hex"</span>))

a= 0x1c5db869a64<span class="hljs-built_in">fc</span>002c8e05674ee4a2bbf9224898bb739026227631384760d8f1003749625766177219aff81f5c71f52a80be1467e494e03abf7f86edfd6d94b07d45<span class="hljs-built_in">cd</span>8ad67866e62ada1a27c06b3d168e13a4aa47800019ee861613a22429a95da14cace342c0fcac16d94bbcddb15197a2d3ce5bc6a45b50bdbeeee2db317c5

b= 0x1c5db869a64<span class="hljs-built_in">fc</span>002c8e05674ee4a2bbf9224898bb739026227631384760d8f1003749625766177219aff81f5c71f52a80be1467e494e03abf7f86edfd6d94b07d45<span class="hljs-built_in">cd</span>8ad67866e62ada1a27c06b3d168e13a4aa47800019ee861613a22429a95da14cace342c0fcac16d94bbcddb15197a2d3ce5bc6a45b50bdbeeee2db317c5

c = 0x4be8e2be903156548ce7557137aff5012337a44337d1e4ac5d998a10c3a9522636e7004bc5defe9df50948fae5dc6252e9853559647283<span class="hljs-built_in">fc</span>191ed8e291e53493f8eba8ecfb65ca85a2782355841170e9f44b6317d173a55865f5e79<span class="hljs-built_in">cd</span>63621122a75d46b49a68bfd04dbc67737e08c07f7fddf4ed4d8c89e11e5efaf879d01dd

d = 0x4a02d85926feb2db2281335ac468ce3<span class="hljs-built_in">fc</span>1824881610e052d2ad233b0a2cbce224beaf3c7d77eb82441d37e2253cf07b0c6cad19b7b1168766b3c3ab8ed3408726a22a07eac33c5a8144658c102b8e774d0ac404173af90997477300385b9b6e002b7858<span class="hljs-built_in">cd</span>299c351062172c526c01d2dc287b37bfc81eb5972330163aa0e073b

n = 0x9f1a727af5b7785285056b713468039e2f109d2887154b52dbe7c182e2c3a0115e1f361ec9cf6f24b7f19e2da7b0d08a489591d106c5c4c8db07390d8d69b5eee31461a0b11b5383fdf42cdbe67c8385858aa3b7689cf8a0dd63387748c03b9918a5f0157d74<span class="hljs-built_in">fc</span>5f02e14bf32561ded9f962b9ce8b0d6d748ab3af671321d5d3
<span class="hljs-built_in">print</span>(<span class="hljs-string">"[+] Hex message : "</span>+TOKEN.encode(<span class="hljs-string">"hex"</span>))
sign = a*b*c*d % n

<span class="hljs-built_in">print</span>(<span class="hljs-string">"[+] signature :"</span>+str(hex(sign))[:-1])
</code></pre>
<br>
<img src="https://raw.githubusercontent.com/kabilan1290/crypto/main/challenge/foobarCTF/winning.png">
<br>
<p>Flag:
GLUG{fl4g_15_53rv3d_xD_E9644V2GG0}</p>
