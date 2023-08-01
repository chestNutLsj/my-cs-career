---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
OS管理进程
需要知道的信息 ^TlgaGeO5

进程位置 ^wytsg8in

进程属性 ^4xUUnzzq

取决于内存管理方案 ^S1KeJGHu

最简单（只有内存）：
进程映像保存在连续的内存块中 ^w4wNj0LZ

引入磁盘：
OS需要知道进程在内存和磁盘中的位置 ^McMcA7xq

引入分页和虚存：
进程映像一部分在内存一部分在外存，OS维护进程在每页中的位置 ^REVKDsZO

PCB管理 ^4Xy5iRhx

进程标识信息 ^ozqh7MPi

处理器状态信息 ^SsSpFPXu

进程控制信息 ^PdC1usE1

该进程PID ^Wni81zHk

父进程PID ^0owJu0s0

用户标识符UID ^xhhqZxgB

用户可见寄存器 ^UE2XDXKm

控制与状态寄存器，如PC、运算条件码、中断状态信息 ^BpJRgCOH

栈指针 ^UFcAHb01

调度和状态信息：进程运行状态、优先级、调度信息、等待事件的标识 ^fpCkbi1G

与其他进程关联的数据结构 ^ybgO5aYK

进程间通信 ^QGCWnrZ0

进程访问内存和可执行指令的权限 ^wsqQZCPI

存储管理，包含描述分配给进程的虚存的段表或页表指针 ^PrLLQzqx

资源所有权和使用情况 ^36Qoq7Oz

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/1.9.12",
	"elements": [
		{
			"id": "sLWd2b91SypXJ2YLj5TUh",
			"type": "rectangle",
			"x": -468.9940946848221,
			"y": 118.96366964473475,
			"width": 159,
			"height": 85,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"seed": 1777238494,
			"version": 211,
			"versionNonce": 1537685150,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "TlgaGeO5"
				},
				{
					"id": "DFUb164bUIrlY0Bo_7IaD",
					"type": "arrow"
				},
				{
					"id": "0N84MdefUaePyEuRtspK1",
					"type": "arrow"
				}
			],
			"updated": 1690884364444,
			"link": null,
			"locked": false
		},
		{
			"id": "TlgaGeO5",
			"type": "text",
			"x": -459.49404127906035,
			"y": 136.46366964473475,
			"width": 139.99989318847656,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 638159326,
			"version": 222,
			"versionNonce": 2122549826,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884364445,
			"link": null,
			"locked": false,
			"text": "OS管理进程\n需要知道的信息",
			"rawText": "OS管理进程\n需要知道的信息",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 43,
			"containerId": "sLWd2b91SypXJ2YLj5TUh",
			"originalText": "OS管理进程\n需要知道的信息",
			"lineHeight": 1.25
		},
		{
			"id": "G1s2myioruWMFJ4KJfmM1",
			"type": "rectangle",
			"x": -219.99409468482207,
			"y": -33.23633035526524,
			"width": 140,
			"height": 67,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"seed": 469562270,
			"version": 199,
			"versionNonce": 1245868190,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "wytsg8in"
				},
				{
					"id": "DFUb164bUIrlY0Bo_7IaD",
					"type": "arrow"
				},
				{
					"id": "xyk6nC1ATFDuRjTS-6d33",
					"type": "arrow"
				}
			],
			"updated": 1690884402876,
			"link": null,
			"locked": false
		},
		{
			"id": "wytsg8in",
			"type": "text",
			"x": -189.99406416724395,
			"y": -12.236330355265238,
			"width": 79.99993896484375,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 513889630,
			"version": 166,
			"versionNonce": 131920386,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884364445,
			"link": null,
			"locked": false,
			"text": "进程位置",
			"rawText": "进程位置",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "G1s2myioruWMFJ4KJfmM1",
			"originalText": "进程位置",
			"lineHeight": 1.25
		},
		{
			"type": "rectangle",
			"version": 217,
			"versionNonce": 899978370,
			"isDeleted": false,
			"id": "AdqVOhOJ-LUzSrz_Xx89V",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -219.99409468482207,
			"y": 371.16366964473474,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 140,
			"height": 67,
			"seed": 926021726,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "4xUUnzzq"
				},
				{
					"id": "0N84MdefUaePyEuRtspK1",
					"type": "arrow"
				},
				{
					"id": "s2jJtPd3oNC-zDCyfCfNQ",
					"type": "arrow"
				}
			],
			"updated": 1690884397852,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 204,
			"versionNonce": 959668674,
			"isDeleted": false,
			"id": "4xUUnzzq",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -189.99406416724395,
			"y": 392.16366964473474,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 79.99993896484375,
			"height": 25,
			"seed": 1833909406,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884364445,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "进程属性",
			"rawText": "进程属性",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "AdqVOhOJ-LUzSrz_Xx89V",
			"originalText": "进程属性",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"id": "DFUb164bUIrlY0Bo_7IaD",
			"type": "arrow",
			"x": -356.59377822912586,
			"y": 117.96366964473475,
			"width": 135.5996835443038,
			"height": 115.80666666666667,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1454023490,
			"version": 689,
			"versionNonce": 796791646,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884364445,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					86.59968354430379,
					-114.5
				],
				[
					135.5996835443038,
					-115.80666666666667
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "sLWd2b91SypXJ2YLj5TUh",
				"gap": 1,
				"focus": 0
			},
			"endBinding": {
				"elementId": "G1s2myioruWMFJ4KJfmM1",
				"gap": 1,
				"focus": 0
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "0N84MdefUaePyEuRtspK1",
			"type": "arrow",
			"x": -367.83471968482206,
			"y": 204.96366964473475,
			"width": 146.840625,
			"height": 197.80666666666667,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 672812766,
			"version": 640,
			"versionNonce": 497408386,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884364445,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					97.84062499999999,
					196.5
				],
				[
					146.840625,
					197.80666666666667
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "sLWd2b91SypXJ2YLj5TUh",
				"gap": 1,
				"focus": 0
			},
			"endBinding": {
				"elementId": "AdqVOhOJ-LUzSrz_Xx89V",
				"gap": 1,
				"focus": 0
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "xyk6nC1ATFDuRjTS-6d33",
			"type": "arrow",
			"x": -75.76954366784997,
			"y": 3.3176400138348185,
			"width": 208.46124893158174,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 915383618,
			"version": 1558,
			"versionNonce": 210606082,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "S1KeJGHu"
				}
			],
			"updated": 1690884403473,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					89.21993044227958,
					0
				],
				[
					208.46124893158174,
					0
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "G1s2myioruWMFJ4KJfmM1",
				"focus": 0.09116329460000169,
				"gap": 4.2245510169721
			},
			"endBinding": {
				"elementId": "utlXblMe2xBnYGRlWajDE",
				"focus": 0,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "S1KeJGHu",
			"type": "text",
			"x": 100.63823279145909,
			"y": -52.78307060759161,
			"width": 144.00027465820312,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1743461762,
			"version": 476,
			"versionNonce": 1707978590,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "取决于内存管理方案",
			"rawText": "取决于内存管理方案",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 14,
			"containerId": "xyk6nC1ATFDuRjTS-6d33",
			"originalText": "取决于内存管理方案",
			"lineHeight": 1.25
		},
		{
			"type": "rectangle",
			"version": 447,
			"versionNonce": 1789787522,
			"isDeleted": false,
			"id": "x0b0HHuhOTxzfDozRJGav",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 251.7656501195815,
			"y": -148.38235998616517,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 215,
			"height": 86,
			"seed": 1917573314,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "w4wNj0LZ"
				},
				{
					"id": "v4GbTVTuhXjIizRGgG2QE",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 536,
			"versionNonce": 1566315422,
			"isDeleted": false,
			"id": "w4wNj0LZ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 259.2657264135268,
			"y": -142.88235998616517,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 199.99984741210938,
			"height": 75,
			"seed": 714567810,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "最简单（只有内存）：\n进程映像保存在连续的\n内存块中",
			"rawText": "最简单（只有内存）：\n进程映像保存在连续的内存块中",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "x0b0HHuhOTxzfDozRJGav",
			"originalText": "最简单（只有内存）：\n进程映像保存在连续的内存块中",
			"lineHeight": 1.25,
			"baseline": 68
		},
		{
			"type": "rectangle",
			"version": 707,
			"versionNonce": 1444147166,
			"isDeleted": false,
			"id": "E_Hpe2mCoHBPmk-HSGFxs",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 251.7656501195815,
			"y": -39.18235998616518,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 262,
			"height": 85,
			"seed": 1437948510,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "McMcA7xq"
				},
				{
					"id": "50Ab8CMibfYbLknvAPVBZ",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 808,
			"versionNonce": 961152258,
			"isDeleted": false,
			"id": "McMcA7xq",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 259.42574533442524,
			"y": -34.18235998616518,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 246.6798095703125,
			"height": 75,
			"seed": 1763073694,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "引入磁盘：\nOS需要知道进程在内存和磁\n盘中的位置",
			"rawText": "引入磁盘：\nOS需要知道进程在内存和磁盘中的位置",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "E_Hpe2mCoHBPmk-HSGFxs",
			"originalText": "引入磁盘：\nOS需要知道进程在内存和磁盘中的位置",
			"lineHeight": 1.25,
			"baseline": 68
		},
		{
			"type": "rectangle",
			"version": 518,
			"versionNonce": 25564354,
			"isDeleted": false,
			"id": "3yQ0eh5WJ7-FTt5REnsIG",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 251.7656501195815,
			"y": 69.01764001383481,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 314,
			"height": 85,
			"seed": 1371106562,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "REVKDsZO"
				},
				{
					"id": "km5N-f5enVREkXDns2E8o",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 671,
			"versionNonce": 1361317982,
			"isDeleted": false,
			"id": "REVKDsZO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 258.76574167231587,
			"y": 74.01764001383481,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 299.99981689453125,
			"height": 75,
			"seed": 667450562,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "引入分页和虚存：\n进程映像一部分在内存一部分在外\n存，OS维护进程在每页中的位置",
			"rawText": "引入分页和虚存：\n进程映像一部分在内存一部分在外存，OS维护进程在每页中的位置",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3yQ0eh5WJ7-FTt5REnsIG",
			"originalText": "引入分页和虚存：\n进程映像一部分在内存一部分在外存，OS维护进程在每页中的位置",
			"lineHeight": 1.25,
			"baseline": 68
		},
		{
			"id": "s2jJtPd3oNC-zDCyfCfNQ",
			"type": "arrow",
			"x": -63.619560496330685,
			"y": 406.9076411357335,
			"width": 196.31126576006244,
			"height": 0.8099988781013394,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 340837634,
			"version": 313,
			"versionNonce": 1493379294,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "4Xy5iRhx"
				}
			],
			"updated": 1690884398257,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					77.06994727076028,
					0.8099988781012826
				],
				[
					196.31126576006244,
					0.8099988781013394
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "AdqVOhOJ-LUzSrz_Xx89V",
				"focus": 0.0390289196151882,
				"gap": 16.37453418849138
			},
			"endBinding": {
				"elementId": "DFSjS6t2-AvQiORtlB3ws",
				"focus": -5.412954679203897e-16,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "4Xy5iRhx",
			"type": "text",
			"x": 63.37024190181182,
			"y": 313.9239987781908,
			"width": 64.51211547851562,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1009080194,
			"version": 50,
			"versionNonce": 1207258178,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "PCB管理",
			"rawText": "PCB管理",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 14,
			"containerId": "s2jJtPd3oNC-zDCyfCfNQ",
			"originalText": "PCB管理",
			"lineHeight": 1.25
		},
		{
			"type": "rectangle",
			"version": 396,
			"versionNonce": 1443238110,
			"isDeleted": false,
			"id": "iyL87ectsMrXUN84EVEy2",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 253.69170526373176,
			"y": 251.01764001383486,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 140,
			"height": 67,
			"seed": 1264134174,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "ozqh7MPi"
				},
				{
					"id": "kUD8ASRYd-VO1tp-SYt9G",
					"type": "arrow"
				},
				{
					"id": "BBXIeTGojUul3pLgLktpV",
					"type": "arrow"
				},
				{
					"id": "1PP7EJ4CeWTxWc8lmS3FD",
					"type": "arrow"
				},
				{
					"id": "DlTNYuHJA6phChLbREnSh",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 401,
			"versionNonce": 29783042,
			"isDeleted": false,
			"id": "ozqh7MPi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 263.69175104009895,
			"y": 272.01764001383486,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 119.99990844726562,
			"height": 25,
			"seed": 477787230,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "进程标识信息",
			"rawText": "进程标识信息",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "iyL87ectsMrXUN84EVEy2",
			"originalText": "进程标识信息",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"type": "rectangle",
			"version": 343,
			"versionNonce": 1970804126,
			"isDeleted": false,
			"id": "va0mmnUmJyE-bThoAwuT-",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 253.69170526373176,
			"y": 374.21764001383485,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 140,
			"height": 67,
			"seed": 758266462,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "SsSpFPXu"
				},
				{
					"id": "0YoFLRT84p_Jzo4ASPsbY",
					"type": "arrow"
				},
				{
					"id": "7rZ4l7MqGsBazUwfoMuOd",
					"type": "arrow"
				},
				{
					"id": "oZpDc0ql8OOHpVcXHO9S8",
					"type": "arrow"
				},
				{
					"id": "t2UpYbO1GXoF0awuKBty1",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 334,
			"versionNonce": 1307843394,
			"isDeleted": false,
			"id": "SsSpFPXu",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 263.69175104009895,
			"y": 382.71764001383485,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 119.99990844726562,
			"height": 50,
			"seed": 2113355422,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "处理器状态信\n息",
			"rawText": "处理器状态信息",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "va0mmnUmJyE-bThoAwuT-",
			"originalText": "处理器状态信息",
			"lineHeight": 1.25,
			"baseline": 43
		},
		{
			"type": "rectangle",
			"version": 349,
			"versionNonce": 401035870,
			"isDeleted": false,
			"id": "B-GncjoqMjeW0jz0acaLS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 253.69170526373176,
			"y": 573.4176400138348,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 140,
			"height": 35,
			"seed": 1934304450,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "PdC1usE1"
				},
				{
					"id": "dcCOpX01UUCwZ1ntdVsDH",
					"type": "arrow"
				},
				{
					"id": "-_bHCTZvMRTRP1du7FOYe",
					"type": "arrow"
				},
				{
					"id": "R7c80GlQeJIKvrVBoEfyI",
					"type": "arrow"
				},
				{
					"id": "3YZs32walbfuxzHpS-PK7",
					"type": "arrow"
				},
				{
					"id": "tGiq_ikVc3FbV1ko1qf50",
					"type": "arrow"
				},
				{
					"id": "CGexPsQenvuFq3SEiX6_n",
					"type": "arrow"
				},
				{
					"id": "UDgd7qLZwS1GU6H3cfPpb",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 334,
			"versionNonce": 984877698,
			"isDeleted": false,
			"id": "PdC1usE1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 263.69175104009895,
			"y": 578.4176400138348,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 119.99990844726562,
			"height": 25,
			"seed": 1707949186,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "进程控制信息",
			"rawText": "进程控制信息",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "B-GncjoqMjeW0jz0acaLS",
			"originalText": "进程控制信息",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"id": "DFSjS6t2-AvQiORtlB3ws",
			"type": "ellipse",
			"x": 133.69170526373176,
			"y": 393.62673092292573,
			"width": 30,
			"height": 28.181818181818187,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 638006750,
			"version": 221,
			"versionNonce": 1765915010,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "s2jJtPd3oNC-zDCyfCfNQ",
					"type": "arrow"
				},
				{
					"id": "kUD8ASRYd-VO1tp-SYt9G",
					"type": "arrow"
				},
				{
					"id": "0YoFLRT84p_Jzo4ASPsbY",
					"type": "arrow"
				},
				{
					"id": "dcCOpX01UUCwZ1ntdVsDH",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false
		},
		{
			"id": "kUD8ASRYd-VO1tp-SYt9G",
			"type": "arrow",
			"x": 163.69170526373176,
			"y": 407.71764001383485,
			"width": 89,
			"height": 121.30666666666667,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1562171870,
			"version": 531,
			"versionNonce": 1677187806,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377219,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					-120
				],
				[
					89,
					-121.30666666666667
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "DFSjS6t2-AvQiORtlB3ws",
				"focus": 0.9543082992671585,
				"gap": 1
			},
			"endBinding": {
				"elementId": "iyL87ectsMrXUN84EVEy2",
				"focus": 6.888255954588285e-16,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "0YoFLRT84p_Jzo4ASPsbY",
			"type": "arrow",
			"x": 163.69170526373176,
			"y": 407.71764001383485,
			"width": 89,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 485564034,
			"version": 531,
			"versionNonce": 2083835678,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377219,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					0
				],
				[
					89,
					0
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "DFSjS6t2-AvQiORtlB3ws",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "va0mmnUmJyE-bThoAwuT-",
				"focus": 0,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "dcCOpX01UUCwZ1ntdVsDH",
			"type": "arrow",
			"x": 163.69170526373176,
			"y": 407.71764001383485,
			"width": 89,
			"height": 181.30666666666662,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 848405470,
			"version": 585,
			"versionNonce": 558447454,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377219,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					180
				],
				[
					89,
					181.30666666666662
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "DFSjS6t2-AvQiORtlB3ws",
				"focus": -0.9788980783828101,
				"gap": 1
			},
			"endBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": 2.9950166247923128e-15,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "Wni81zHk",
			"type": "text",
			"x": 483.69170526373176,
			"y": 231.31764001383488,
			"width": 79.77615356445312,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 184123458,
			"version": 182,
			"versionNonce": 939518082,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "BBXIeTGojUul3pLgLktpV",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "该进程PID",
			"rawText": "该进程PID",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "该进程PID",
			"lineHeight": 1.25
		},
		{
			"id": "0owJu0s0",
			"type": "text",
			"x": 483.69170526373176,
			"y": 274.51764001383486,
			"width": 79.77615356445312,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1883165982,
			"version": 171,
			"versionNonce": 112429122,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "1PP7EJ4CeWTxWc8lmS3FD",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "父进程PID",
			"rawText": "父进程PID",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "父进程PID",
			"lineHeight": 1.25
		},
		{
			"id": "xhhqZxgB",
			"type": "text",
			"x": 483.69170526373176,
			"y": 317.71764001383485,
			"width": 112.62422180175781,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1319420766,
			"version": 185,
			"versionNonce": 1163735042,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "DlTNYuHJA6phChLbREnSh",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "用户标识符UID",
			"rawText": "用户标识符UID",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "用户标识符UID",
			"lineHeight": 1.25
		},
		{
			"id": "UE2XDXKm",
			"type": "text",
			"x": 483.69170526373176,
			"y": 354.51764001383486,
			"width": 112.00021362304688,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2109431490,
			"version": 208,
			"versionNonce": 402382786,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "7rZ4l7MqGsBazUwfoMuOd",
					"type": "arrow"
				}
			],
			"updated": 1690884376626,
			"link": null,
			"locked": false,
			"text": "用户可见寄存器",
			"rawText": "用户可见寄存器",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "用户可见寄存器",
			"lineHeight": 1.25
		},
		{
			"id": "BpJRgCOH",
			"type": "text",
			"x": 483.69170526373176,
			"y": 397.71764001383485,
			"width": 388.8807373046875,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 369205634,
			"version": 265,
			"versionNonce": 1252956034,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "oZpDc0ql8OOHpVcXHO9S8",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "控制与状态寄存器，如PC、运算条件码、中断状态信息",
			"rawText": "控制与状态寄存器，如PC、运算条件码、中断状态信息",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "控制与状态寄存器，如PC、运算条件码、中断状态信息",
			"lineHeight": 1.25
		},
		{
			"id": "UFcAHb01",
			"type": "text",
			"x": 483.69170526373176,
			"y": 440.91764001383484,
			"width": 48.000091552734375,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1384626398,
			"version": 183,
			"versionNonce": 1303664450,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "t2UpYbO1GXoF0awuKBty1",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "栈指针",
			"rawText": "栈指针",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "栈指针",
			"lineHeight": 1.25
		},
		{
			"id": "fpCkbi1G",
			"type": "text",
			"x": 483.69170526373176,
			"y": 477.7176400138348,
			"width": 496.0009460449219,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1841513026,
			"version": 321,
			"versionNonce": 829169410,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "-_bHCTZvMRTRP1du7FOYe",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "调度和状态信息：进程运行状态、优先级、调度信息、等待事件的标识",
			"rawText": "调度和状态信息：进程运行状态、优先级、调度信息、等待事件的标识",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "调度和状态信息：进程运行状态、优先级、调度信息、等待事件的标识",
			"lineHeight": 1.25
		},
		{
			"id": "ybgO5aYK",
			"type": "text",
			"x": 483.69170526373176,
			"y": 514.5176400138348,
			"width": 192.0003662109375,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1322889346,
			"version": 222,
			"versionNonce": 1878396802,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1690884377227,
			"link": null,
			"locked": false,
			"text": "与其他进程关联的数据结构",
			"rawText": "与其他进程关联的数据结构",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "与其他进程关联的数据结构",
			"lineHeight": 1.25
		},
		{
			"id": "QGCWnrZ0",
			"type": "text",
			"x": 483.69170526373176,
			"y": 551.3176400138348,
			"width": 80.00015258789062,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 179313310,
			"version": 220,
			"versionNonce": 1695401602,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "3YZs32walbfuxzHpS-PK7",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "进程间通信",
			"rawText": "进程间通信",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "进程间通信",
			"lineHeight": 1.25
		},
		{
			"id": "wsqQZCPI",
			"type": "text",
			"x": 483.69170526373176,
			"y": 604.1176400138348,
			"width": 240.00045776367188,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 824939394,
			"version": 235,
			"versionNonce": 1381344834,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "tGiq_ikVc3FbV1ko1qf50",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "进程访问内存和可执行指令的权限",
			"rawText": "进程访问内存和可执行指令的权限",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "进程访问内存和可执行指令的权限",
			"lineHeight": 1.25
		},
		{
			"id": "PrLLQzqx",
			"type": "text",
			"x": 483.69170526373176,
			"y": 647.3176400138348,
			"width": 400.0007629394531,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1876577090,
			"version": 268,
			"versionNonce": 1274076766,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1690884377229,
			"link": null,
			"locked": false,
			"text": "存储管理，包含描述分配给进程的虚存的段表或页表指针",
			"rawText": "存储管理，包含描述分配给进程的虚存的段表或页表指针",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "存储管理，包含描述分配给进程的虚存的段表或页表指针",
			"lineHeight": 1.25
		},
		{
			"id": "36Qoq7Oz",
			"type": "text",
			"x": 483.69170526373176,
			"y": 690.5176400138348,
			"width": 160.00030517578125,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 136638878,
			"version": 206,
			"versionNonce": 1546550722,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "UDgd7qLZwS1GU6H3cfPpb",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false,
			"text": "资源所有权和使用情况",
			"rawText": "资源所有权和使用情况",
			"fontSize": 16,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 14,
			"containerId": null,
			"originalText": "资源所有权和使用情况",
			"lineHeight": 1.25
		},
		{
			"id": "BBXIeTGojUul3pLgLktpV",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 258.69945819565305,
			"width": 89,
			"height": 17.38181818181819,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1637517890,
			"version": 518,
			"versionNonce": 965091230,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377223,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					-14.181818181818187
				],
				[
					89,
					-17.38181818181819
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "iyL87ectsMrXUN84EVEy2",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "Wni81zHk",
				"focus": 0.20336732895833637,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "1PP7EJ4CeWTxWc8lmS3FD",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 284.51764001383486,
			"width": 89,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 851097182,
			"version": 518,
			"versionNonce": 958168030,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377223,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					0
				],
				[
					89,
					0
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "iyL87ectsMrXUN84EVEy2",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "0owJu0s0",
				"focus": 0,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "DlTNYuHJA6phChLbREnSh",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 310.3358218320167,
			"width": 89,
			"height": 17.38181818181819,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 2026990082,
			"version": 544,
			"versionNonce": 1713197086,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377224,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					14.181818181818187
				],
				[
					89,
					17.38181818181819
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "iyL87ectsMrXUN84EVEy2",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "xhhqZxgB",
				"focus": -0.2649207361660896,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "7rZ4l7MqGsBazUwfoMuOd",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 381.89945819565304,
			"width": 89,
			"height": 17.38181818181819,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 2014904414,
			"version": 540,
			"versionNonce": 477731934,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377225,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					-14.181818181818187
				],
				[
					89,
					-17.38181818181819
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "va0mmnUmJyE-bThoAwuT-",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "UE2XDXKm",
				"focus": 0.2638401820040121,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "oZpDc0ql8OOHpVcXHO9S8",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 407.71764001383485,
			"width": 89,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1827797122,
			"version": 511,
			"versionNonce": 302145694,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377225,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					0
				],
				[
					89,
					0
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "va0mmnUmJyE-bThoAwuT-",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "BpJRgCOH",
				"focus": 0,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "t2UpYbO1GXoF0awuKBty1",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 433.53582183201667,
			"width": 89,
			"height": 17.38181818181819,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1996634818,
			"version": 543,
			"versionNonce": 443063518,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377226,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					14.181818181818187
				],
				[
					89,
					17.38181818181819
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "va0mmnUmJyE-bThoAwuT-",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "UFcAHb01",
				"focus": -0.13314862513901146,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "-_bHCTZvMRTRP1du7FOYe",
			"type": "arrow",
			"x": 344.0417052637317,
			"y": 572.4176400138348,
			"width": 139.65,
			"height": 84.7,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1188263618,
			"version": 642,
			"versionNonce": 1799180574,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377227,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					89.65,
					-81.5
				],
				[
					139.65,
					-84.7
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": -2.5007157856656277e-16,
				"gap": 1
			},
			"endBinding": {
				"elementId": "fpCkbi1G",
				"focus": 0.613482208612484,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "R7c80GlQeJIKvrVBoEfyI",
			"type": "arrow",
			"x": 357.60837193039845,
			"y": 572.4176400138348,
			"width": 126.08333333333334,
			"height": 47.900000000000006,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1047290626,
			"version": 566,
			"versionNonce": 1609661790,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377227,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					76.08333333333334,
					-41.5
				],
				[
					126.08333333333334,
					-47.900000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": 1.6101209374883408e-16,
				"gap": 1
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "3YZs32walbfuxzHpS-PK7",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 578.0085491047438,
			"width": 89,
			"height": 16.69090909090901,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 2062863134,
			"version": 630,
			"versionNonce": 1263324994,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377228,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					-7.090909090909008
				],
				[
					89,
					-16.69090909090901
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": -8.100762525912758e-15,
				"gap": 1
			},
			"endBinding": {
				"elementId": "QGCWnrZ0",
				"focus": 0.4343896088975387,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "tGiq_ikVc3FbV1ko1qf50",
			"type": "arrow",
			"x": 394.69170526373176,
			"y": 603.8267309229258,
			"width": 89,
			"height": 10.290909090909008,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1919862978,
			"version": 569,
			"versionNonce": 1429575426,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377228,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					39,
					7.090909090909008
				],
				[
					89,
					10.290909090909008
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": 8.100762525912758e-15,
				"gap": 1
			},
			"endBinding": {
				"elementId": "wsqQZCPI",
				"focus": -0.4343896088975412,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "CGexPsQenvuFq3SEiX6_n",
			"type": "arrow",
			"x": 357.60837193039845,
			"y": 609.4176400138348,
			"width": 126.08333333333334,
			"height": 47.900000000000006,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 488288514,
			"version": 593,
			"versionNonce": 2117035714,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377229,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					76.08333333333334,
					41.5
				],
				[
					126.08333333333334,
					47.900000000000006
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": -1.6101209374883408e-16,
				"gap": 1
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "UDgd7qLZwS1GU6H3cfPpb",
			"type": "arrow",
			"x": 344.0417052637317,
			"y": 609.4176400138348,
			"width": 139.65,
			"height": 91.1,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 746686210,
			"version": 588,
			"versionNonce": 356606622,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377230,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					89.65,
					81.5
				],
				[
					139.65,
					91.1
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "B-GncjoqMjeW0jz0acaLS",
				"focus": 2.5007157856656277e-16,
				"gap": 1
			},
			"endBinding": {
				"elementId": "36Qoq7Oz",
				"focus": -0.6056786889740613,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "utlXblMe2xBnYGRlWajDE",
			"type": "ellipse",
			"x": 133.69170526373176,
			"y": -11.304206265253555,
			"width": 28.07394485584973,
			"height": 29.243692558176747,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1206568158,
			"version": 303,
			"versionNonce": 637266946,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "v4GbTVTuhXjIizRGgG2QE",
					"type": "arrow"
				},
				{
					"id": "50Ab8CMibfYbLknvAPVBZ",
					"type": "arrow"
				},
				{
					"id": "km5N-f5enVREkXDns2E8o",
					"type": "arrow"
				},
				{
					"id": "xyk6nC1ATFDuRjTS-6d33",
					"type": "arrow"
				}
			],
			"updated": 1690884376627,
			"link": null,
			"locked": false
		},
		{
			"id": "v4GbTVTuhXjIizRGgG2QE",
			"type": "arrow",
			"x": 161.7656501195815,
			"y": 3.3176400138348185,
			"width": 88.99999999999999,
			"height": 106.49555555555555,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1302360386,
			"version": 987,
			"versionNonce": 578248414,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377231,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					-105.5
				],
				[
					88.99999999999999,
					-106.49555555555555
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "utlXblMe2xBnYGRlWajDE",
				"focus": 0.9300891786461313,
				"gap": 1
			},
			"endBinding": {
				"elementId": "x0b0HHuhOTxzfDozRJGav",
				"focus": 2.0539421254319322e-16,
				"gap": 1
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "50Ab8CMibfYbLknvAPVBZ",
			"type": "arrow",
			"x": 161.7656501195815,
			"y": 3.3176400138348185,
			"width": 89,
			"height": 0,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 190958402,
			"version": 1061,
			"versionNonce": 1210307358,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377231,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					0
				],
				[
					89,
					0
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "utlXblMe2xBnYGRlWajDE",
				"focus": 0,
				"gap": 1
			},
			"endBinding": {
				"elementId": "E_Hpe2mCoHBPmk-HSGFxs",
				"focus": 0,
				"gap": 1.0000000000000284
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "km5N-f5enVREkXDns2E8o",
			"type": "arrow",
			"x": 161.7656501195815,
			"y": 3.3176400138348185,
			"width": 89,
			"height": 105.75748792270531,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1052560322,
			"version": 928,
			"versionNonce": 548912990,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1690884377232,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					40,
					105
				],
				[
					89,
					105.75748792270531
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "utlXblMe2xBnYGRlWajDE",
				"focus": -0.9294907111696339,
				"gap": 1
			},
			"endBinding": {
				"elementId": "3yQ0eh5WJ7-FTt5REnsIG",
				"focus": 2.065694839235667e-16,
				"gap": 1.0000000000000284
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "WzTHwNQv2qHNGsCYcZ3D8",
			"type": "line",
			"x": 70.01477513561917,
			"y": -268.7735546193474,
			"width": 219.88586657520693,
			"height": 15.269851845500483,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1350837150,
			"version": 17,
			"versionNonce": 865084830,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1690884386866,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					219.88586657520693,
					15.269851845500483
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 1,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
		"currentItemFontSize": 16,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 505.38359092951123,
		"scrollY": 432.68726248390846,
		"zoom": {
			"value": 0.6525003339516109
		},
		"currentItemRoundness": "round",
		"gridSize": null,
		"currentStrokeOptions": null,
		"previousGridSize": null,
		"frameRendering": {
			"enabled": true,
			"clip": true,
			"name": true,
			"outline": true
		}
	},
	"files": {}
}
```
%%