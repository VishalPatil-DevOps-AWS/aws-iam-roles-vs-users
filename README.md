<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>aws-iam-roles-vs-users | Vishal Patil</title>
<meta name="description" content="Hands-on AWS lab: IAM Roles vs IAM Users, EC2 to S3, cross-account access and GitHub Actions OIDC.">
<style>
:root{--bg:#eef2f7;--ink:#0f172a;--mute:#475569;--line:#d5dce8;--card:#fff;--orange:#e8590c;--teal:#0f766e;--red:#be123c;--dark:#0f1b2d;--serif:Georgia,"Iowan Old Style","Times New Roman",serif;--sans:system-ui,-apple-system,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;--mono:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace}
*{box-sizing:border-box}
html{scroll-behavior:smooth}
body{margin:0;background:var(--bg);color:var(--ink);font:17px/1.65 var(--sans)}
a{color:var(--teal)}
a:focus-visible,button:focus-visible{outline:3px solid var(--orange);outline-offset:3px}
.wrap{max-width:1040px;margin:0 auto;padding:0 24px}
nav{padding:18px 0;border-bottom:1px solid var(--line)}
nav .wrap{display:flex;justify-content:space-between;align-items:center;gap:16px;flex-wrap:wrap}
nav b{font-family:var(--mono);font-size:15px}
nav div a{margin-left:18px;color:var(--ink);text-decoration:none;font-size:15px}
nav div a:hover{color:var(--orange)}

/* hero */
.hero{padding:64px 0 56px}
.hero .wrap{display:grid;grid-template-columns:1.15fr .85fr;gap:48px;align-items:center}
h1{font:700 clamp(40px,6vw,68px)/1.04 var(--serif);margin:0 0 20px;letter-spacing:-.02em}
h1 span{color:var(--orange);font-style:italic}
.lead{font-size:20px;color:var(--mute);max-width:34em;margin:0 0 28px}
.btns{display:flex;flex-wrap:wrap;gap:12px}
.btn{display:inline-block;padding:12px 20px;border-radius:8px;font-weight:600;text-decoration:none;border:2px solid var(--ink);color:var(--ink);transition:background .2s,color .2s}
.btn:hover{background:var(--ink);color:#fff}
.btn.main{background:var(--ink);color:#fff}
.btn.main:hover{background:var(--orange);border-color:var(--orange)}

/* credential demo */
.demo{display:grid;gap:14px}
.cred{background:var(--dark);color:#e8eef7;border-radius:16px;padding:22px 24px;display:flex;gap:20px;align-items:center;position:relative;overflow:hidden}
.cred small{display:block;color:#9fb0cf;font-size:13px}
.cred strong{font:600 18px var(--sans);display:block;margin-bottom:2px}
.cred code{font:13px var(--mono);color:#7ee0d4;word-break:break-all}
.ring{flex:none;width:84px;height:84px;position:relative}
.ring svg{width:84px;height:84px;transform:rotate(-90deg)}
.ring circle{fill:none;stroke-width:8}
.ring .bg{stroke:#2a3a55}
.ring .fg{stroke:#2dd4bf;stroke-linecap:round;stroke-dasharray:238.76;stroke-dashoffset:0;transition:stroke-dashoffset 1s linear}
.ring span{position:absolute;inset:0;display:grid;place-items:center;font:700 20px var(--mono)}
.cred.key{background:#fff;color:var(--ink);border:2px dashed var(--red)}
.cred.key small{color:var(--mute)}
.cred.key code{color:var(--red)}
.cred.key .never{flex:none;width:84px;text-align:center;font:700 34px var(--serif);color:var(--red)}
.flash{position:absolute;right:16px;top:12px;font-size:12px;font-weight:700;color:#2dd4bf;opacity:0;transition:opacity .3s}
.flash.on{opacity:1}

section{padding:56px 0;border-top:1px solid var(--line)}
h2{font:700 clamp(28px,4vw,38px)/1.15 var(--serif);margin:0 0 12px;letter-spacing:-.01em}
.sub{color:var(--mute);max-width:40em;margin:0 0 32px}

/* flow */
.flow{background:var(--dark);border-radius:18px;padding:36px 28px;color:#fff}
.nodes{display:grid;grid-template-columns:1fr 36px 1fr 36px 1fr 36px 1fr;align-items:center;gap:6px;position:relative}
.node{background:#fff;color:var(--ink);border-radius:12px;padding:16px 10px;text-align:center}
.node b{display:block;font-size:17px}
.node small{color:var(--mute);font-size:12.5px;line-height:1.3;display:block;margin-top:2px}
.node:nth-child(3){background:#e5defc}.node:nth-child(5){background:#cfe6ff}.node:nth-child(7){background:#d3f3dc}
.link{height:4px;background:repeating-linear-gradient(90deg,var(--orange) 0 8px,transparent 8px 14px);background-size:28px 4px;animation:march 1s linear infinite}
@keyframes march{to{background-position:28px 0}}
.dot{position:absolute;top:50%;left:6%;width:16px;height:16px;margin-top:-8px;border-radius:50%;background:#fff;border:4px solid var(--orange);animation:travel 5s ease-in-out infinite}
@keyframes travel{0%{left:12%;opacity:0}10%{opacity:1}90%{opacity:1}100%{left:88%;opacity:0}}
.cap{margin:22px 0 0;color:#c4d0e6;font-size:15px;text-align:center}

/* compare */
.cmp{display:grid;grid-template-columns:1fr 1fr;gap:0;border:1px solid var(--line);border-radius:14px;overflow:hidden;background:var(--card)}
.cmp div{padding:22px 26px}
.cmp div+div{border-left:1px solid var(--line);background:#f1faf8}
.cmp h3{margin:0 0 10px;font:700 22px var(--serif)}
.cmp ul{margin:0;padding-left:20px}
.cmp li{margin:6px 0}
.note{margin-top:18px;padding:14px 18px;border-left:4px solid var(--orange);background:#fff7ed;border-radius:0 8px 8px 0}

/* steps */
.steps{list-style:none;margin:0;padding:0;display:grid;gap:18px;counter-reset:s}
.steps li{counter-increment:s;display:grid;grid-template-columns:44px 1fr;gap:16px}
.steps li::before{content:counter(s);width:44px;height:44px;border-radius:50%;background:var(--ink);color:#fff;display:grid;place-items:center;font:700 18px var(--mono)}
.steps h3{margin:6px 0 6px;font-size:18px}
.steps p{margin:0 0 8px;color:var(--mute)}
pre{margin:0;background:var(--dark);color:#d8e2f2;border-radius:10px;padding:14px 16px;overflow-x:auto;font:14px/1.55 var(--mono);position:relative}
.copy{position:absolute;right:8px;top:8px;background:#26364f;color:#fff;border:0;border-radius:6px;padding:4px 10px;font:12px var(--sans);cursor:pointer}
.copy:hover{background:var(--orange)}
pre.has{padding-right:70px}

/* two col */
.two{display:grid;grid-template-columns:1fr 1fr;gap:28px}
.two h3{font:700 22px var(--serif);margin:0 0 8px}
.two p{color:var(--mute);margin:0 0 12px}

/* tree */
.tree{background:var(--card);border:1px solid var(--line);color:var(--ink)}
.tree b{color:var(--teal)}

/* author */
.author{display:grid;grid-template-columns:repeat(3,1fr);gap:18px}
.author div{background:var(--card);border:1px solid var(--line);border-radius:12px;padding:20px 22px}
.author h3{margin:0 0 6px;font:700 20px var(--serif)}
.author a{word-break:break-all;font-size:15px}
footer{padding:32px 0 48px;border-top:1px solid var(--line);color:var(--mute);font-size:15px;text-align:center}

@media(max-width:820px){
 .hero .wrap,.two,.author,.cmp{grid-template-columns:1fr}
 .cmp div+div{border-left:0;border-top:1px solid var(--line)}
 .nodes{grid-template-columns:1fr;gap:0}
 .link{width:4px;height:26px;margin:4px auto;background:repeating-linear-gradient(180deg,var(--orange) 0 8px,transparent 8px 14px);animation:none}
 .dot{display:none}
}
@media(prefers-reduced-motion:reduce){.link,.dot{animation:none}.ring .fg{transition:none}html{scroll-behavior:auto}}
</style>
</head>
<body>

<nav><div class="wrap">
  <b>VishalPatil-DevOps-AWS / aws-iam-roles-vs-users</b>
  <div><a href="#flow">How it works</a><a href="#start">Quick start</a><a href="#more">More labs</a><a href="#author">Author</a></div>
</div></nav>

<header class="hero"><div class="wrap">
  <div>
    <h1>IAM roles, not <span>access keys</span></h1>
    <p class="lead">A hands-on AWS lab that shows when to use an IAM role instead of an IAM user, and how an EC2 instance can read S3 without a single key in its code.</p>
    <div class="btns">
      <a class="btn main" href="https://github.com/VishalPatil-DevOps-AWS" target="_blank" rel="noopener">View on GitHub</a>
      <a class="btn" href="https://dev.to/vishal_patil_aws" target="_blank" rel="noopener">Read on dev.to</a>
      <a class="btn" href="https://www.linkedin.com/in/vishalpatil-devops-aws/" target="_blank" rel="noopener">LinkedIn</a>
    </div>
  </div>
  <div class="demo" aria-label="Temporary credential compared with a permanent access key">
    <div class="cred">
      <div class="ring"><svg viewBox="0 0 84 84"><circle class="bg" cx="42" cy="42" r="38"/><circle class="fg" id="fg" cx="42" cy="42" r="38"/></svg><span id="t">0:10</span></div>
      <div><strong>IAM role session</strong><small>Temporary credential</small><code>ASIA…EXPIRES-SOON</code></div>
      <span class="flash" id="flash">Refreshed by AWS</span>
    </div>
    <div class="cred key">
      <div class="never" aria-hidden="true">&infin;</div>
      <div><strong>IAM user access key</strong><small>Valid until someone revokes it</small><code>AKIA…NEVER-EXPIRES</code></div>
    </div>
  </div>
</div></header>

<section id="flow"><div class="wrap">
  <h2>How an instance gets access without keys</h2>
  <p class="sub">The role is attached to the instance. AWS Security Token Service hands the instance short-lived credentials, and the SDK refreshes them before they expire.</p>
  <div class="flow">
    <div class="nodes">
      <div class="node"><b>EC2 instance</b><small>no keys in code</small></div><div class="link"></div>
      <div class="node"><b>AWS STS</b><small>issues temporary credentials</small></div><div class="link"></div>
      <div class="node"><b>IAM role</b><small>ec2-s3-readonly</small></div><div class="link"></div>
      <div class="node"><b>Amazon S3</b><small>read-only access</small></div>
      <div class="dot"></div>
    </div>
    <p class="cap">The trust policy decides who can assume the role. The permission policy decides what the role can do.</p>
  </div>
</div></section>

<section><div class="wrap">
  <h2>User or role?</h2>
  <p class="sub">The short version of the decision.</p>
  <div class="cmp">
    <div><h3>IAM user</h3><ul><li>Long-term password or access keys</li><li>You rotate keys by hand</li><li>A leaked key works until revoked</li><li>Best for people, ideally through SSO</li></ul></div>
    <div><h3>IAM role</h3><ul><li>Temporary credentials from STS</li><li>Rotation is automatic</li><li>A stolen credential expires on its own</li><li>Best for services, apps, CI/CD and other accounts</li></ul></div>
  </div>
  <p class="note"><b>Rule of thumb:</b> if an application needs AWS access, use a role, not access keys.</p>
</div></section>

<section id="start"><div class="wrap">
  <h2>Quick start</h2>
  <p class="sub">You need AWS CLI v2, permission to create IAM roles, and an EC2 instance you can SSH into.</p>
  <ol class="steps">
    <li><div><h3>Clone the repo</h3>
      <pre class="has"><button class="copy">Copy</button>git clone https://github.com/VishalPatil-DevOps-AWS/aws-iam-roles-vs-users.git
cd aws-iam-roles-vs-users/scripts</pre></div></li>
    <li><div><h3>Create the role</h3><p>Creates the role with the EC2 trust policy and attaches S3 read-only access.</p>
      <pre class="has"><button class="copy">Copy</button>./01-create-role.sh</pre></div></li>
    <li><div><h3>Create the instance profile</h3><p>EC2 uses an instance profile that wraps the role.</p>
      <pre class="has"><button class="copy">Copy</button>./02-create-instance-profile.sh</pre></div></li>
    <li><div><h3>Attach it to your instance</h3><p>In the console: EC2, Instances, Actions, Security, Modify IAM role, then pick <code>ec2-s3-readonly</code>.</p></div></li>
    <li><div><h3>Verify on the instance</h3><p>The ARN should contain <code>assumed-role</code>, not an access key.</p>
      <pre class="has"><button class="copy">Copy</button>aws sts get-caller-identity
aws s3 ls</pre></div></li>
    <li><div><h3>Clean up</h3><p>Detach the role from the instance first, then remove the lab resources.</p>
      <pre class="has"><button class="copy">Copy</button>./cleanup.sh</pre></div></li>
  </ol>
</div></section>

<section id="more"><div class="wrap">
  <h2>Going further</h2>
  <div class="two">
    <div><h3>Cross-account access</h3><p>The role lives in account B and trusts account A. Account A assumes it and gets temporary credentials, with every call logged in CloudTrail.</p>
      <pre class="has"><button class="copy">Copy</button>./assume-cross-account-role.sh \
  &lt;ACCOUNT_B_ID&gt; prod-readonly my-session</pre></div>
    <div><h3>GitHub Actions with OIDC</h3><p>Let the workflow assume a role instead of storing AWS keys in secrets. Keep the <code>sub</code> condition so only your repository can assume it.</p>
      <pre>examples/github-actions-oidc.yml
policies/trust-policy-github-oidc.json</pre></div>
  </div>
  <h3 style="font:700 22px var(--serif);margin:40px 0 12px">What is in the repository</h3>
<pre class="tree">aws-iam-roles-vs-users/
├── <b>assets/</b>      animated banner and diagram
├── <b>docs/</b>        troubleshooting.md
├── <b>examples/</b>    github-actions-oidc.yml
├── <b>policies/</b>    trust policies: ec2, cross-account, github-oidc
├── <b>scripts/</b>     create role, instance profile, verify, cleanup
├── LICENSE
└── README.md</pre>
</div></section>

<section id="author"><div class="wrap">
  <h2>Author</h2>
  <p class="sub">Vishal Patil, DevOps and AWS.</p>
  <div class="author">
    <div><h3>LinkedIn</h3><a href="https://www.linkedin.com/in/vishalpatil-devops-aws/" target="_blank" rel="noopener">linkedin.com/in/vishalpatil-devops-aws</a></div>
    <div><h3>dev.to</h3><a href="https://dev.to/vishal_patil_aws" target="_blank" rel="noopener">dev.to/vishal_patil_aws</a></div>
    <div><h3>GitHub</h3><a href="https://github.com/VishalPatil-DevOps-AWS" target="_blank" rel="noopener">github.com/VishalPatil-DevOps-AWS</a></div>
  </div>
</div></section>

<footer>Released under the MIT License. If this helped you, star the repo.</footer>

<script>
(function(){
  var C=238.76, total=10, left=total, fg=document.getElementById('fg'), t=document.getElementById('t'), fl=document.getElementById('flash');
  function draw(){t.textContent='0:'+(left<10?'0':'')+left;fg.style.strokeDashoffset=C*(1-left/total)}
  draw();
  setInterval(function(){
    left--;
    if(left<0){left=total;fl.classList.add('on');setTimeout(function(){fl.classList.remove('on')},1400)}
    draw();
  },1000);
  document.querySelectorAll('.copy').forEach(function(b){
    b.addEventListener('click',function(){
      var txt=b.parentNode.textContent.replace(/^Copy|Copied!/,'').trim();
      var done=function(){b.textContent='Copied!';setTimeout(function(){b.textContent='Copy'},1400)};
      if(navigator.clipboard){navigator.clipboard.writeText(txt).then(done)}else{done()}
    });
  });
})();
</script>
</body>
</html>
