AnyProxy
==========

> AnyProxy 4.x正在Beta中，欢迎试用！文档：http://anyproxy.io/4.x/

[![NPM version][npm-image]][npm-url]
[![node version][node-image]][node-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/anyproxy.svg?style=flat-square
[npm-url]: https://npmjs.org/package/anyproxy
[node-image]: https://img.shields.io/badge/node.js-%3E=_0.10-green.svg?style=flat-square
[node-url]: http://nodejs.org/download/
[download-image]: https://img.shields.io/npm/dm/anyproxy.svg?style=flat-square
[download-url]: https://npmjs.org/package/anyproxy

A fully configurable HTTP/HTTPS proxy in NodeJS.

Home page : [AnyProxy.io](http://anyproxy.io)

(Chinese in this doc is nothing but translation of some key points. Be relax if you dont understand.)

<img src="http://gtms04.alicdn.com/tps/i4/TB1XfxDHpXXXXXpapXX20ySQVXX-512-512.png" width="250" height="250" alt="anyproxy logo" />

特性
------------
* 支持https明文代理
* 支持低网速模拟
* 支持二次开发，可以用javascript控制代理的全部流程，搭建前端个性化调试环境
* 提供web版界面，观测请求情况
* 中文文档：
    * [代理服务器的新轮子](https://github.com/alibaba/anyproxy/wiki/%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E6%96%B0%E8%BD%AE%E5%AD%90%EF%BC%9Aanyproxy) ，介绍AnyProxy的设计初衷，以及它的开放式设计可以解决什么问题。
    * [HTTPS相关教程](https://github.com/alibaba/anyproxy/wiki/HTTPS%E7%9B%B8%E5%85%B3%E6%95%99%E7%A8%8B)，讲解如何配置AnyProxy的https特性
* 中文主页：[http://anyproxy.io](http://anyproxy.io/cn)

Feature
------------
* work as http or https proxy
* fully configurable, you could modify a request at any stage with your customized javascript code
* when working as https proxy, AnyProxy could intercept https requests for any domain without complaint by browser (after you trust its root CA)
* a web interface for you to watch realtime request details, where html with (almost) any charset could be shown correctly

Quick Start
--------------

### step 1 - install

* install [NodeJS](http://nodejs.org/)
* ``npm install -g anyproxy`` , may require ``sudo``
* python is optional, it will be OK if you get some error about it during installing.

### step 2 - start proxy server

* start with default settings : ``anyproxy``
* ...or start with a specific port:  ``anyproxy --port 8001``
* ...start with a rule file: ``anyproxy --rule ./rule_sample/rule_allow_CORS.js``

### step 3 - set up proxy settings in your browser , or system ,or mobile devices ,etc

* default proxy should be 127.0.0.1:8001 , or you_ip_address:8001 on remote devices

### step 4 - launch web interface

* visit [http://127.0.0.1:8002](http://127.0.0.1:8002) ,you will see realtime requests in your browser
* be sure to use web interface with modern browsers

![screenshot](http://gtms01.alicdn.com/tps/i1/TB1IdgqGXXXXXa9apXXLExM2pXX-854-480.gif)



Rule module
-------------------
* Rule module is the specialty for AnyProxy. Think it as a middleware, you could write your own code to hack requests at any stage, no matter it is about to response or the proxy just gets the request. In this way, AnyProxy would be flexible to meet your own demands.

* It's highly recommended to read this guide before using: [What is rule file and how to write one ?](https://github.com/alibaba/anyproxy/wiki/What-is-rule-file-and-how-to-write-one)

* An entire scheme of rule file could be found at [./rule_sample/rule__blank.js](https://github.com/alibaba/anyproxy/blob/master/rule_sample/rule__blank.js). Besides, there are some samples at [./rule_sample](https://github.com/alibaba/anyproxy/tree/master/rule_sample). That may help you a lot when writing your own rule files.


Https features
----------------
After configuring rootCA, AnyProxy could help to decrypt https requests, whose approach is also called Man-In-The-Middle(MITM).

A guide about configuring https features is here : [https://github.com/alibaba/anyproxy/wiki/How-to-config-https-proxy](https://github.com/alibaba/anyproxy/wiki/How-to-config-https-proxy)

HTTPS配置中文教程 : [https://github.com/alibaba/anyproxy/wiki/HTTPS%E7%9B%B8%E5%85%B3%E6%95%99%E7%A8%8B](https://github.com/alibaba/anyproxy/wiki/HTTPS%E7%9B%B8%E5%85%B3%E6%95%99%E7%A8%8B)


Others
-----------------

#### to save request data
* to save request data to local file, use ``` anyproxy --file /path/to/file ```
* anyproxy uses [nedb](https://github.com/louischatriot/nedb) to save request data. Since NeDB's persistence uses an append-only format, you may get some redundant record in local file. For those dupplicated ones with the same id, just use the lastest line of record.
* [TrafficeRecorder](https://github.com/ottomao/TrafficRecorder) is another tool based on anyproxy to help recording all request data, including header and body. You may have a try.

#### throttling
* for instance, ``` anyproxy --throttle 10 ``` sets the speed limit to 10kb/s (kbyte/sec)
* this is just a rough throttling for downstream, not for network simulation

#### work as a module for nodejs
* use it as a module and develop your own proxy.

```
npm install anyproxy --save
```

```javascript
var proxy = require("anyproxy");

//create cert when you want to use https features
//please manually trust this rootCA when it is the first time you run it
!proxy.isRootCAFileExists() && proxy.generateRootCA();

var options = {
    type          : "http",
    port          : 8001,
    hostname      : "localhost",
    rule          : require("path/to/my/ruleModule.js"),
    dbFile        : null,  // optional, save request data to a specified file, will use in-memory db if not specified
    webPort       : 8002,  // optional, port for web interface
    socketPort    : 8003,  // optional, internal port for web socket, replace this when it is conflict with your own service
    throttle      : 10,    // optional, speed limit in kb/s
    disableWebInterface : false, //optional, set it when you don't want to use the web interface
    setAsGlobalProxy : false, //set anyproxy as your system proxy
    silent        : false //optional, do not print anything into terminal. do not set it when you are still debugging.
};
new proxy.proxyServer(options);

```

Contact
-----------------

* Please feel free to [raise issue](https://github.com/alibaba/anyproxy/issues), or give us some advice. :)
* AnyProxy用户旺旺群：1203077233


License
-----------------

Apache License
                           Version 2.0, January 2004
                        https://www.apache.org/licenses/

   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
      outstanding shares, or (iii) beneficial ownership of such entity.

      "You" (or "Your") shall mean an individual or Legal Entity
      exercising permissions granted by this License.

      "Source" form shall mean the preferred form for making modifications,
      including but not limited to software source code, documentation
      source, and configuration files.

      "Object" form shall mean any form resulting from mechanical
      transformation or translation of a Source form, including but
      not limited to compiled object code, generated documentation,
      and conversions to other media types.

      "Work" shall mean the work of authorship, whether in Source or
      Object form, made available under the License, as indicated by a
      copyright notice that is included in or attached to the work
      (an example is provided in the Appendix below).

      "Derivative Works" shall mean any work, whether in Source or Object
      form, that is based on (or derived from) the Work and for which the
      editorial revisions, annotations, elaborations, or other modifications
      represent, as a whole, an original work of authorship. For the purposes
      of this License, Derivative Works shall not include works that remain
      separable from, or merely link (or bind by name) to the interfaces of,
      the Work and Derivative Works thereof.

      "Contribution" shall mean any work of authorship, including
      the original version of the Work and any modifications or additions
      to that Work or Derivative Works thereof, that is intentionally
      submitted to Licensor for inclusion in the Work by the copyright owner
      or by an individual or Legal Entity authorized to submit on behalf of
      the copyright owner. For the purposes of this definition, "submitted"
      means any form of electronic, verbal, or written communication sent
      to the Licensor or its representatives, including but not limited to
      communication on electronic mailing lists, source code control systems,
      and issue tracking systems that are managed by, or on behalf of, the
      Licensor for the purpose of discussing and improving the Work, but
      excluding communication that is conspicuously marked or otherwise
      designated in writing by the copyright owner as "Not a Contribution."

      "Contributor" shall mean Licensor and any individual or Legal Entity
      on behalf of whom a Contribution has been received by Licensor and
      subsequently incorporated within the Work.

   2. Grant of Copyright License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      copyright license to reproduce, prepare Derivative Works of,
      publicly display, publicly perform, sublicense, and distribute the
      Work and such Derivative Works in Source or Object form.

   3. Grant of Patent License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      (except as stated in this section) patent license to make, have made,
      use, offer to sell, sell, import, and otherwise transfer the Work,
      where such license applies only to those patent claims licensable
      by such Contributor that are necessarily infringed by their
      Contribution(s) alone or by combination of their Contribution(s)
      with the Work to which such Contribution(s) was submitted. If You
      institute patent litigation against any entity (including a
      cross-claim or counterclaim in a lawsuit) alleging that the Work
      or a Contribution incorporated within the Work constitutes direct
      or contributory patent infringement, then any patent licenses
      granted to You under this License for that Work shall terminate
      as of the date such litigation is filed.

   4. Redistribution. You may reproduce and distribute copies of the
      Work or Derivative Works thereof in any medium, with or without
      modifications, and in Source or Object form, provided that You
      meet the following conditions:

      (a) You must give any other recipients of the Work or
          Derivative Works a copy of this License; and

      (b) You must cause any modified files to carry prominent notices
          stating that You changed the files; and

      (c) You must retain, in the Source form of any Derivative Works
          that You distribute, all copyright, patent, trademark, and
          attribution notices from the Source form of the Work,
          excluding those notices that do not pertain to any part of
          the Derivative Works; and

      (d) If the Work includes a "NOTICE" text file as part of its
          distribution, then any Derivative Works that You distribute must
          include a readable copy of the attribution notices contained
          within such NOTICE file, excluding those notices that do not
          pertain to any part of the Derivative Works, in at least one
          of the following places: within a NOTICE text file distributed
          as part of the Derivative Works; within the Source form or
          documentation, if provided along with the Derivative Works; or,
          within a display generated by the Derivative Works, if and
          wherever such third-party notices normally appear. The contents
          of the NOTICE file are for informational purposes only and
          do not modify the License. You may add Your own attribution
          notices within Derivative Works that You distribute, alongside
          or as an addendum to the NOTICE text from the Work, provided
          that such additional attribution notices cannot be construed
          as modifying the License.

      You may add Your own copyright statement to Your modifications and
      may provide additional or different license terms and conditions
      for use, reproduction, or distribution of Your modifications, or
      for any such Derivative Works as a whole, provided Your use,
      reproduction, and distribution of the Work otherwise complies with
      the conditions stated in this License.

   5. Submission of Contributions. Unless You explicitly state otherwise,
      any Contribution intentionally submitted for inclusion in the Work
      by You to the Licensor shall be under the terms and conditions of
      this License, without any additional terms or conditions.
      Notwithstanding the above, nothing herein shall supersede or modify
      the terms of any separate license agreement you may have executed
      with Licensor regarding such Contributions.

   6. Trademarks. This License does not grant permission to use the trade
      names, trademarks, service marks, or product names of the Licensor,
      except as required for reasonable and customary use in describing the
      origin of the Work and reproducing the content of the NOTICE file.

   7. Disclaimer of Warranty. Unless required by applicable law or
      agreed to in writing, Licensor provides the Work (and each
      Contributor provides its Contributions) on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
      implied, including, without limitation, any warranties or conditions
      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
      PARTICULAR PURPOSE. You are solely responsible for determining the
      appropriateness of using or redistributing the Work and assume any
      risks associated with Your exercise of permissions under this License.

   8. Limitation of Liability. In no event and under no legal theory,
      whether in tort (including negligence), contract, or otherwise,
      unless required by applicable law (such as deliberate and grossly
      negligent acts) or agreed to in writing, shall any Contributor be
      liable to You for damages, including any direct, indirect, special,
      incidental, or consequential damages of any character arising as a
      result of this License or out of the use or inability to use the
      Work (including but not limited to damages for loss of goodwill,
      work stoppage, computer failure or malfunction, or any and all
      other commercial damages or losses), even if such Contributor
      has been advised of the possibility of such damages.

   9. Accepting Warranty or Additional Liability. While redistributing
      the Work or Derivative Works thereof, You may choose to offer,
      and charge a fee for, acceptance of support, warranty, indemnity,
      or other liability obligations and/or rights consistent with this
      License. However, in accepting such obligations, You may act only
      on Your own behalf and on Your sole responsibility, not on behalf
      of any other Contributor, and only if You agree to indemnify,
      defend, and hold each Contributor harmless for any liability
      incurred by, or claims asserted against, such Contributor by reason
      of your accepting any such warranty or additional liability.

   END OF TERMS AND CONDITIONS

   APPENDIX: How to apply the Apache License to your work.

      To apply the Apache License to your work, attach the following
      boilerplate notice, with the fields enclosed by brackets "[]"
      replaced with your own identifying information. (Don't include
      the brackets!)  The text should be enclosed in the appropriate
      comment syntax for the file format. We also recommend that a
      file or class name and description of purpose be included on the
      same "printed page" as the copyright notice for easier
      identification within third-party archives.

   Copyright 2019 Rolando Gopez Lacuata.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       https://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

