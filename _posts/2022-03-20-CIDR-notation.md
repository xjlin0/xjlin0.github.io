---
layout: post
title: "HTML table tricks"
description: "Control tables without JavaScript"
category: tech
tags: [network, CIDR, HTML]
---
{% include JB/setup %} 

<link rel="stylesheet" href="/assets/xcss/table.css" type="text/css" media="screen" charset="utf-8">


### Need to show tables? <img src="/assets/imgs/change_math.png"  alt="Various Edna Mode Styles" width="40%"/>

HTML tables, some people love them, some people hate them.  But there are surely quite some tricks we can do about it.

### selectable columns

Modified from [DBMS comparison](https://www.sql-workbench.eu/dbms_comparison.html){:target="_blank"}, when we have checkboxes such as `input#on-category` with the id, we can toggle the display of the table columns with this

```css
body:not(:has(input#on-category:checked)) table.toggle tr :nth-child(1),
body:not(:has(input#on-name:checked))     table.toggle tr :nth-child(2),
body:not(:has(input#on-type:checked))     table.toggle tr :nth-child(3),
body:not(:has(input#on-use:checked))      table.toggle tr :nth-child(4) {
  display: none;
}
```

Note: Please be aware of that, if there are cells with `colspan`, toggling the columns containing first cells will remove the entire row.  For example, the Polysporin row below. 

<div class="checkboxes" style="display:flex;">
    <input style="width: 2%;" id="on-category" type="checkbox" checked />
    <label for="on-category">Toggle Column Category</label>
    <div style="flex-grow: 1;"></div>
    <input style="width: 2%;" id="on-name" type="checkbox" checked />
    <label for="on-name">Toggle Column ame</label>
    <div style="flex-grow: 1;"></div>
    <input style="width: 2%;" id="on-type" type="checkbox" checked />
    <label for="on-type">Toggle Column Type</label>
    <div style="flex-grow: 1;"></div>
    <input style="width: 2%;" id="on-use" type="checkbox" checked />
    <label for="on-use">Toggle Column Use</label>
</div>


<table class="toggle">
	<caption>OTC (Over-The-Counter) Products</caption>
	<thead style="background-color: silver;">
	<tr>
			<th>Category</th>
			<th>Name</th>
			<th>Type</th>
			<th>Use</th>
		</tr>
  </thead>
  <tbody align="left">
		<tr>
			<td rowspan="5" style="vertical-align: middle; background-color: Beige;">Acne medication</td>
			<td>Benzoyl peroxide 2.5%</td>
			<td>gel/cream/lotion</td>
			<td></td>
		</tr>
		<tr>
			<td>Benzoyl peroxide 5%</td>
			<td>gel/cream/lotion</td>
			<td></td>
		</tr>
		<tr>
			<td>Benzoyl peroxide 5%</td>
			<td>wash</td>
			<td></td>
		</tr>
		<tr>
			<td>Benzoyl peroxide 10%</td>
			<td>wash</td>
			<td></td>
		</tr>
		<tr>
			<td>Salicylic acid foam</td>
			<td>SalAc Foam</td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="2" style="vertical-align: middle; background-color: Bisque;">Antibiotics &amp; Antiseptics</td>
			<td colspan="3" style="text-align: center; background-color: Beige;">Double antibiotic ointment (Polysporin)</td>
		</tr>
		<tr>
			<td>Hibiclens cleanser</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="3" style="vertical-align: middle; background-color: Beige;">Antifungal medication</td>
			<td>Clotrimazole</td>
			<td>cream/solution</td>
			<td>twice daily</td>
		</tr>
		<tr>
			<td>Miconazole</td>
			<td>cream/solution</td>
			<td></td>
		</tr>
		<tr>
			<td>Lamisil</td>
			<td>cream/solution</td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="4" style="vertical-align: middle; background-color: Bisque;">Itch-relief medication</td>
			<td>Sarna</td>
			<td>lotion</td>
			<td></td>
		</tr>
		<tr>
			<td>Sarnol-HC (with 1% hydrocortisone)</td>
			<td>lotion</td>
			<td></td>
		</tr>
		<tr>
			<td colspan="3" style="text-align: center; background-color: Beige;">Aveeno Anti-Itch Concentrated Lotion</td>
		</tr>
		<tr>
			<td>Benadryl</td>
			<td>cream</td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="2" style="vertical-align: middle; background-color: Beige;">Keloid treatment (silicon gel)</td>
			<td>Morelle SOS sheets</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>Band-Aid Scar Therapy</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="3" style="vertical-align: middle; background-color: Bisque;">Rash medication/treatment</td>
			<td>Hydrocortisone 1%</td>
			<td>cream/ointment</td>
			<td></td>
		</tr>
		<tr>
			<td>Aveeno Bath treatment</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>Bluboro/Domeboro soaks</td>
			<td></td>
			<td></td>
		</tr>
	  <tr>
			<td rowspan="2" style="vertical-align: middle; background-color: Beige;">Skin protectant</td>
			<td>Zinc oxide cream/paste (Desitin)</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>BodyGlide</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td rowspan="2" style="vertical-align: middle; background-color: Bisque;">Wart treatment</td>
			<td>Wart removal liquids (17% salicylic acid)</td>
			<td>solution</td>
			<td></td>
		</tr>
		<tr>
			<td colspan="3" style="text-align: center; background-color: Beige;">Mediplast patches (40% salicylic acid)</td>
		</tr>
		<tr>
			<td rowspan="3" style="vertical-align: middle; background-color: Beige;">Miscellaneous</td>
			<td>Ammonium lactate 12% lotion</td>
			<td>lotion</td>
			<td></td>
		</tr>
		<tr>
			<td>Ela-Max 4% topical anesthetic</td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>Minoxidil 2%, 5%</td>
			<td>solution</td>
			<td></td>
		</tr>
  </tbody>
</table>

<hr>
### Sticky table heads
To make the table head always stay even under scrolling, please use CSS tricks on `position`. It's very useful for the long tables.

```css
table {
	width: 100%;
	position: relative;
	border-collapse: collapse;
}

th {
	ackground: white;
	position: sticky;
	top: 0;
}
```

Example: CIDR notation([Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)) table.
<hr/>
<table cellspacing="0" border="0" style="width: 100%; position: relative; border-collapse: collapse;">
	<thead>
		<tr align="left">
			<th style="background: white; position: sticky; top: 0;">CIDR prefix length</th>
			<th style="background: white; position: sticky; top: 0;">Dotted Decimal Netmask</th>
			<th style="background: white; position: sticky; top: 0;">Number of Classful Networks</th>
			<th style="background: white; position: sticky; top: 0;">Number of Usable IPs</th>
			<th style="background: white; position: sticky; top: 0;">Note</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>/1</td>
			<td>128.0.0.0</td>
			<td>128 As</td>
			<td>2147483646</td>
			<td>2^(32-1)-2</td>
		</tr>
		<tr>
			<td>/2</td>
			<td>192.0.0.0</td>
			<td>64 As</td>
			<td>1073741822</td>
			<td>2^(32-2)-2</td>
		</tr>
		<tr>
			<td>/3</td>
			<td>224.0.0.0</td>
			<td>32 As</td>
			<td>536870910</td>
			<td>2^(32-3)-2</td>
		</tr>
		<tr>
			<td>/4</td>
			<td>240.0.0.0</td>
			<td>16 As</td>
			<td>268435454</td>
			<td>2^(32-4)-2</td>
		</tr>
		<tr>
			<td>/5</td>
			<td>248.0.0.0</td>
			<td>8 As</td>
			<td>134217726</td>
			<td>2^(32-5)-2</td>
		</tr>
		<tr>
			<td>/6</td>
			<td>252.0.0.0</td>
			<td>4 As</td>
			<td>67108862</td>
			<td>2^(32-6)-2</td>
		</tr>
		<tr>
			<td>/7</td>
			<td>254.0.0.0</td>
			<td>2 As</td>
			<td>33554430</td>
			<td>2^(32-7)-2</td>
		</tr>
		<tr>
			<td>/8</td>
			<td>255.0.0.0</td>
			<td>1 A or 256 Bs</td>
			<td>16777214</td>
			<td>2^(32-8)-2</td>
		</tr>
		<tr>
			<td>/9</td>
			<td>255.128.0.0</td>
			<td>128 Bs</td>
			<td>8388606</td>
			<td>2^(32-9)-2</td>
		</tr>
		<tr>
			<td>/10</td>
			<td>255.192.0.0</td>
			<td>64 Bs</td>
			<td>4194302</td>
			<td>2^(32-10)-2</td>
		</tr>
		<tr>
			<td>/11</td>
			<td>255.224.0.0</td>
			<td>32 Bs</td>
			<td>2097150</td>
			<td>2^(32-11)-2</td>
		</tr>
		<tr>
			<td>/12</td>
			<td>255.240.0.0</td>
			<td>16 Bs</td>
			<td>1048574</td>
			<td>2^(32-12)-2</td>
		</tr>
		<tr>
			<td>/13</td>
			<td>255.248.0.0</td>
			<td>8 Bs</td>
			<td>524286</td>
			<td>2^(32-13)-2</td>
		</tr>
		<tr>
			<td>/14</td>
			<td>255.252.0.0</td>
			<td>4 Bs</td>
			<td>262142</td>
			<td>2^(32-14)-2</td>
		</tr>
		<tr>
			<td>/15</td>
			<td>255.254.0.0</td>
			<td>2 Bs</td>
			<td>131070</td>
			<td>2^(32-15)-2</td>
		</tr>
		<tr>
			<td>/16</td>
			<td>255.255.0.0</td>
			<td>1 B or 256 Cs</td>
			<td>65534</td>
			<td>2^(32-16)-2</td>
		</tr>
		<tr>
			<td>/17</td>
			<td>255.255.128.0</td>
			<td>128 Cs</td>
			<td>32766</td>
			<td>2^(32-17)-2</td>
		</tr>
		<tr>
			<td>/18</td>
			<td>255.255.192.0</td>
			<td>64 Cs</td>
			<td>16382</td>
			<td>2^(32-18)-2</td>
		</tr>
		<tr>
			<td>/19</td>
			<td>255.255.224.0</td>
			<td>32 Cs</td>
			<td>8190</td>
			<td>2^(32-19)-2</td>
		</tr>
		<tr>
			<td>/20</td>
			<td>255.255.240.0</td>
			<td>16 Cs</td>
			<td>4094</td>
			<td>2^(32-20)-2</td>
		</tr>
		<tr>
			<td>/21</td>
			<td>255.255.248.0</td>
			<td>8 Cs</td>
			<td>2046</td>
			<td>2^(32-21)-2</td>
		</tr>
		<tr>
			<td>/22</td>
			<td>255.255.252.0</td>
			<td>4 Cs</td>
			<td>1022</td>
			<td>2^(32-22)-2</td>
		</tr>
		<tr>
			<td>/23</td>
			<td>255.255.254.0</td>
			<td>2 Cs</td>
			<td>510</td>
			<td>2^(32-23)-2</td>
		</tr>
		<tr>
			<td>/24</td>
			<td>255.255.255.0</td>
			<td>1 C</td>
			<td>254</td>
			<td>2^(32-24)-2</td>
		</tr>
		<tr>
			<td>/25</td>
			<td>255.255.255.128</td>
			<td>1/2 C</td>
			<td>126</td>
			<td>2^(32-25)-2</td>
		</tr>
		<tr>
			<td>/26</td>
			<td>255.255.255.192</td>
			<td>1/4 C</td>
			<td>62</td>
			<td>2^(32-26)-2</td>
		</tr>
		<tr>
			<td>/27</td>
			<td>255.255.255.224</td>
			<td>1/8 C</td>
			<td>30</td>
			<td>2^(32-27)-2</td>
		</tr>
		<tr>
			<td>/28</td>
			<td>255.255.255.240</td>
			<td>1/16 C</td>
			<td>14</td>
			<td>2^(32-28)-2</td>
		</tr>
		<tr>
			<td>/29</td>
			<td>255.255.255.248</td>
			<td>1/32 C</td>
			<td>6</td>
			<td>2^(32-29)-2</td>
		</tr>
		<tr>
			<td>/30</td>
			<td>255.255.255.252</td>
			<td>1/64 C</td>
			<td>2</td>
			<td>2^(32-30)-2</td>
		</tr>
		<tr>
			<td>/31</td>
			<td>255.255.255.254</td>
			<td>1/128 C</td>
			<td>0</td>
			<td>2^(32-31)-2</td>
		</tr>
		<tr>
			<td>/32</td>
			<td>255.255.255.255</td>
			<td>1/256 C</td>
			<td>1</td>
			<td>reserved</td>
		</tr>
	</tbody>
</table>
<hr/>

ps. There are [some range of IPv4 address reserved](https://en.wikipedia.org/wiki/Reserved_IP_addresses#IPv4) for special uses.