---

## POST listing

POST https://erp.campsnail.com/api/v2/open/worksheet/getFilterRows

> Body 请求参数

```json
{  
    "appKey": "bcea886729893cdd",  
    "sign": "OWI4YTUxMjY5ZTZlZDdjZmE2MWE1MGViMTIzZDhkYjdmNDJmNWJmZmMxYTE5MDVkY2QwM2Q3Y2Q4ZDFiNzE3Nw==",  
    "worksheetId": "listing",  
    "viewId": "6870ad17ab380365166c817a",  
    "pageSize": 1000,  
    "pageIndex": 1,  
    "listType": 0,  
    "controls": [  
        "rowid",  
        "msku",  
        "ljbqmc",  
        "asin",  
        "parent_asin"  
    ]
}
```

### 请求参数

|名称|位置|类型| 必选  | 说明               |
|---|---|---|-----|------------------|
|Content-Type|header|string| 是   | application/json |
|body|body|object| 是   | none             |
|» appKey|body|string| 是   | 按json示例填         |
|» sign|body|string| 是   | 按json示例填         |
|» worksheetId|body|string| 是   | 表id,按json示例填     |
|» viewId|body|string| 是   | 视图id,按json示例填    |
|» pageSize|body|integer| 是   | 按json示例填         |
|» pageIndex|body|integer| 是   | 按json示例填         |
|» listType|body|integer| 是   | 按json示例填         |
|» controls|body|[string]| 是   | 按json示例填         |


> 返回示例

> 200 Response

```json
{
    "data": {
        "rows": [
            {
                "_id": "6874995ef028e3f26b02bdec",
                "rowid": "6df76a7e-d1a1-4be8-af1b-df7a98429fec",
                "msku": "MMCUS454W410Xmolv",
                "asin": "B0FHGT813S",
                "parent_asin": "B0DD7MM342",
                "ljbqmc": "餐包-无标LB0011组-AMZ-美茶-US-4号",
                "autoid": 0
            },
            {
                "_id": "68749945f028e3f26b02bdad",
                "rowid": "0d01b5a5-9fa8-4fb9-a6f4-7c2e572d8e23",
                "msku": "0MCUS8475853Fpink",
                "asin": "B0FHGRY2K9",
                "parent_asin": "B0DD7MM342",
                "ljbqmc": "餐包-无标LB0011组-AMZ-美茶-US-4号",
                "autoid": 0
            },
            {
                "_id": "6874985af028e3f26b02a4cc",
                "rowid": "b77a2777-42cb-422a-8383-43ce339dd420",
                "msku": "RMCUS473K623Uhuanghua",
                "asin": "B0FHGSR81V",
                "parent_asin": "B0DD7MM342",
                "ljbqmc": "餐包-无标LB0011组-AMZ-美茶-US-4号",
                "autoid": 0
            },
            {
                "_id": "68743ad86c06f79055b6da52",
                "rowid": "f99e78ce-a184-4b6e-a6ac-5646fbdaf633",
                "msku": "FBAYSDDK210805CYT1249453",
                "asin": "B09BZKMXT8",
                "parent_asin": "B09BZKMXT8",
                "ljbqmc": "打底裤-九分高腰4组-AMZ-赢生-US-2号",
                "autoid": 0
            },
            {
                "_id": "6872e61a7e14e6848d3cbbeb",
                "rowid": "7890b989-eb05-4880-90ee-73def2f4f278",
                "msku": "DYDQIFENDADIZNOQDCBJCKB06DL",
                "asin": "B09NT13JJG",
                "parent_asin": "B0DBV3X85L",
                "ljbqmc": "打底裤-七分高腰3组-AMZ-多益得-DE-1号",
                "autoid": 0
            },
            {
                "_id": "6872dfd77e14e6848d3c39fc",
                "rowid": "e32b5c3b-bba9-4558-95c5-df0c62a488aa",
                "msku": "NZDDOBBWUFEN1ZU12005003NL",
                "asin": "B0BP2GFWR9",
                "parent_asin": "B0D6M531QT",
                "ljbqmc": "打底裤-五分高腰1组-AMZ-那云-DE-1号",
                "autoid": 0
            },
            {
                "_id": "6872823dfd5a5fb49cae8bb0",
                "rowid": "456295fb-b230-43c1-a0f6-ae264bb50c0d",
                "msku": "5SAJNUS75702640VINE",
                "asin": "B0FHB2ZR1S",
                "parent_asin": "B0FHB2ZR1S",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b87fd5a5fb49c9be867",
                "rowid": "2340a83d-46af-4015-a75a-72479d136555",
                "msku": "Gnull757M765M_BWBXL",
                "asin": "B0FHB6KX8Z",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b5ffd5a5fb49c9be83b",
                "rowid": "9000ccaa-37e2-4550-ab02-7771ddc5bdc5",
                "msku": "Pnull420W1578_BBCXL",
                "asin": "B0FHB74Q21",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b48fd5a5fb49c9be812",
                "rowid": "43592ed6-28a7-4ffc-b46d-ab687a5adf5d",
                "msku": "Nnull904V081E_BBCM",
                "asin": "B0FHBB937B",
                "parent_asin": "B0FHB63KVQ",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b42fd5a5fb49c9be7ee",
                "rowid": "ff3ebd30-17a6-4a4e-8334-85452842d7ba",
                "msku": "Anull9629440R_BBCS",
                "asin": "B0FHB9LQ36",
                "parent_asin": "B0FHB63KVQ",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b3dfd5a5fb49c9be7d6",
                "rowid": "d15260e9-429a-417b-b27d-f56d318d8262",
                "msku": "Cnull700X557W_BL",
                "asin": "B0FHB6KSLC",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b25fd5a5fb49c9be7b2",
                "rowid": "02f49eac-2cb7-4d36-bb71-8928084ce8d5",
                "msku": "Xnull808E273S_BWBM",
                "asin": "B0FHB629RS",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713b13fd5a5fb49c9be792",
                "rowid": "cc1a98d4-783e-4cbf-8257-6cec21529c71",
                "msku": "Snull935T4793_BWGM",
                "asin": "B0FHB71BGZ",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713af0fd5a5fb49c9be766",
                "rowid": "2bfa2535-001a-4026-8022-bf4433dd795c",
                "msku": "Rnull884W5776_BLWL",
                "asin": "B0FHB77XD3",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713ae5fd5a5fb49c9be74a",
                "rowid": "81bfabd6-443d-4212-9a3c-2f532f6a327b",
                "msku": "Rnull201V617P_BLWS",
                "asin": "B0FHB6KK8R",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713ac2fd5a5fb49c9be71e",
                "rowid": "56ce8ba1-b98c-4f16-95be-a0d2c7aa9c4b",
                "msku": "3null085T307D_BWGS",
                "asin": "B0FHB6FXNJ",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713a9efd5a5fb49c9be6f2",
                "rowid": "9f9f7655-1a97-4cf8-ab9a-70ab681a7841",
                "msku": "Cnull990M9559_BWGL",
                "asin": "B0FHB7B66H",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713a8dfd5a5fb49c9be6d2",
                "rowid": "40b1e154-0e44-44c5-8659-ef77755f0f03",
                "msku": "8null34659254_BWPXL",
                "asin": "B0FHB4R947",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687139fcfd5a5fb49c9be658",
                "rowid": "a1451dc0-4863-443a-aff4-f63733fb1891",
                "msku": "Rnull415X768I_BWPM",
                "asin": "B0FHB5WW8D",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687139ccfd5a5fb49c9be624",
                "rowid": "ef399dfe-827d-4eb6-a8f7-34ffd3e6b1b0",
                "msku": "3null7909064N_BWBL",
                "asin": "B0FHB99DV5",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "6871392dfd5a5fb49c9be5a8",
                "rowid": "28364f0a-6aff-478e-a527-d02c45714a41",
                "msku": "0null016F644R_BLWXL",
                "asin": "B0FHB7VHGH",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713904fd5a5fb49c9be578",
                "rowid": "f0fe6079-42ce-4151-83b3-e6ba347f66f7",
                "msku": "Hnull128H264Z_BWPS",
                "asin": "B0FHB79LPN",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687138dbfd5a5fb49c9be548",
                "rowid": "fec38528-3ae7-4854-b19c-e43e387f8091",
                "msku": "Wnull742M4757_BWGXL",
                "asin": "B0FHB5L947",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687138c4fd5a5fb49c9be524",
                "rowid": "1cbdd8f9-2a8b-4d01-831e-a03f0d7b6da0",
                "msku": "Qnull6763921J_BWBS",
                "asin": "B0FHB92J15",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "6871383afd5a5fb49c9be4b4",
                "rowid": "50253afe-6025-4f64-b19b-cde0a7ce7af4",
                "msku": "Gnull588M6664_BM",
                "asin": "B0FHB6PV3Y",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "6871381dfd5a5fb49c9be486",
                "rowid": "16b7c9e6-a138-41a0-b37f-f4ed8fd3e3ba",
                "msku": "Pnull0998282H_BWPL",
                "asin": "B0FHB6PB69",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687137e2fd5a5fb49c9be44a",
                "rowid": "1fea5ecf-2fc6-4952-a755-d764af0621a7",
                "msku": "Ynull881V597N_BXL",
                "asin": "B0FHB623TV",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "687137d1fd5a5fb49c9be42a",
                "rowid": "711ac6b8-b9b3-477a-9a15-ab7c082ed289",
                "msku": "9null209E190A_BS",
                "asin": "B0FHB9GW2V",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "6871378ffd5a5fb49c9be3ea",
                "rowid": "4c106eef-533c-4a85-9e6c-3fda3bfef968",
                "msku": "Enull017F6687_BLWM",
                "asin": "B0FHBBXQ3C",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713680fd5a5fb49c9be31e",
                "rowid": "90a1f5d6-da05-4384-9770-16359e694c39",
                "msku": "Knull498I139R_BBCL",
                "asin": "B0FHB7WFF7",
                "parent_asin": "",
                "ljbqmc": "打底衣-高领紧身长袖加绒3组-AMZ-橙蝶-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713588fd5a5fb49c9be256",
                "rowid": "87eb7c2e-b488-4bfc-8c5c-a53671dae7aa",
                "msku": "AHLTPROUS7016915Avine10",
                "asin": "B0FHB9ZWGP",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687134cefd5a5fb49c9bdf5a",
                "rowid": "5e2393f9-f42c-49b0-a86f-305a5f808e25",
                "msku": "6SAJNUS723V920RVINE",
                "asin": "B0FH9NHMJ4",
                "parent_asin": "",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            },
            {
                "_id": "6871342dfd5a5fb49c9bdece",
                "rowid": "2d0aa260-5e6c-4538-aaa0-73ac486aed04",
                "msku": "BSAJNUS839J520HVINE",
                "asin": "B0FHB29DL1",
                "parent_asin": "",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            },
            {
                "_id": "687133d9fd5a5fb49c9bde7c",
                "rowid": "ba938d55-afd0-45c4-8f33-72324417d2af",
                "msku": "NHLTPROUS685F653Wvine19",
                "asin": "B0FHB817PF",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687133a9fd5a5fb49c9bde3c",
                "rowid": "1d30c2f4-e724-49ee-be14-45d2cac1e8c2",
                "msku": "7SAJNUS889D652YVINE",
                "asin": "B0FHB22J6Z",
                "parent_asin": "B0FHB22J6Z",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            },
            {
                "_id": "68713399fd5a5fb49c9bde1c",
                "rowid": "296134b6-ca94-4ab6-bc18-480aea0c2247",
                "msku": "RHLTPROUS48658064vine11",
                "asin": "B0FHB6ZMXL",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "6871333ffd5a5fb49c9bddcc",
                "rowid": "5b105bba-329c-44fb-923f-03812fddfc00",
                "msku": "0HLTPROUS65149728vine05",
                "asin": "B0FHB6VJ2M",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "68713304fd5a5fb49c9bdd90",
                "rowid": "733718f5-8316-4bd5-8041-816ab64e7cc7",
                "msku": "MHLTPROUS84153607vine08",
                "asin": "B0FHBB13LN",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687132d5fd5a5fb49c9bdd5c",
                "rowid": "fe5ed342-c590-4a9d-831a-d5c53f811cb1",
                "msku": "9HLTPROUS3392025Nvine28",
                "asin": "B0FHBBPHBM",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687132b2fd5a5fb49c9bdd30",
                "rowid": "b876b6d1-eca2-402f-a325-bac24eeefcab",
                "msku": "KHLTPROUS598W1255vine12",
                "asin": "B0FHB9JKD6",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "6871328ffd5a5fb49c9bdd04",
                "rowid": "99b642fb-129f-4662-9b95-150927f736a4",
                "msku": "BHLTPROUS7954867Kvine07",
                "asin": "B0FHB8QXQ3",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "68713218fd5a5fb49c9bdc9a",
                "rowid": "f477de2e-07fa-4311-9b94-95a2cfa9df34",
                "msku": "IHLTPROUS717K5100vine09",
                "asin": "B0FHB6D4CY",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687131fbfd5a5fb49c9bdc72",
                "rowid": "c74484ff-1cd8-476c-bfb0-4d84b921a75c",
                "msku": "RHLTPROUS400W420Yvine22",
                "asin": "B0FHBBZM3K",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687131defd5a5fb49c9bdc4a",
                "rowid": "bff01228-3e8b-4346-85b4-28fca8119293",
                "msku": "PHLTPROUS286Q9779vine17",
                "asin": "B0FHB951Z8",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "687131b1fd5a5fb49c9bdc1a",
                "rowid": "30f65c42-f118-4a4a-baf4-7bf66e3e44ce",
                "msku": "IHLTPROUS682C102Gvine06",
                "asin": "B0FHB6CY19",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "6871317cfd5a5fb49c9bdbe2",
                "rowid": "188fbc2d-757b-4ab9-abe1-8733b5a3ea0c",
                "msku": "QHLTPROUS365U013Ovine04",
                "asin": "B0FHB9WJK6",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "68713159fd5a5fb49c9bdbb6",
                "rowid": "efe7d1a2-d783-4e2b-b848-dc9c7fdad5e0",
                "msku": "PHLTPROUS205C9857vine01",
                "asin": "B0FHB8JG2Z",
                "parent_asin": "",
                "ljbqmc": "阔腿裤-九分口袋3组-AMZ-羚羊-US-6号",
                "autoid": 0
            },
            {
                "_id": "68713105fd5a5fb49c9bdb5e",
                "rowid": "a3c5a57c-aa5c-4b07-9a26-a0c0d10b647e",
                "msku": "SSAJNUS992E589CVINE",
                "asin": "B0FH9YMMZ1",
                "parent_asin": "B0FH9YMMZ1",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            },
            {
                "_id": "687130dbfd5a5fb49c9bdb22",
                "rowid": "00a233e9-dc48-4c31-befe-7e9373b96484",
                "msku": "LSAJNUS707F712QVINE",
                "asin": "B0FH9YMMYZ",
                "parent_asin": "B0FH9YMMYZ",
                "ljbqmc": "运动裤-九分直筒德绒1组-AMZ-三境-US-1号",
                "autoid": 0
            }
        ],
        "total": 162458
    },
    "success": true,
    "error_code": 1
}
```

### 返回结果

|状态码|状态码含义|说明|数据模型|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|none|Inline|

### 返回数据结构

状态码 **200**

|名称|类型|必选|约束| 中文名    |说明|
|---|---|---|---|--------|---|
|» data|object|true|none|| none   |
|»» rows|[object]|true|none|| none   |
|»»» _id|string|true|none|| none   |
|»»» rowid|string|true|none|| 记录id   |
|»»» ljbqmc|string|true|none|| 链接标签名称 |
|»»» xl|string|true|none|| 销量     |
|»»» autoid|integer|true|none|| none   |
|»»» ljbqbm|string|true|none|| 链接标签编码 |
|»» total|integer|true|none|| none   |
|» success|boolean|true|none|| none   |
|» error_code|integer|true|none|| none   |


