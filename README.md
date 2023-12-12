# Rpc methods
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/f3bbdbd9-97a4-4a13-8ee8-e5b0800b1883)


# Port collection
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/6cf4a67f-0987-4fea-a07f-404d2227beee)


# Edge Bnd Config
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/993e8787-f06d-421a-9129-6ee7da737479)

- org.osgi.service.http.port=8080: port mà felix apache của edge sẽ chạy
- felix.cm.dir: địa chỉ folder lưu config
- openems.data.dir: địa chỉ folder lưu data simulate

## Set up edge scheduler 
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/1e02617a-cb96-4834-a0d4-bd75bd27feb5)
=> Tạo một EMS Component


# Backend
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/bc021131-876a-484a-b12b-21bd118edaaa)

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/5ba10e3a-ddd7-4350-8654-07c56c0c219c)

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/7a795b37-d756-4459-97e5-0284ced0d420)



![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/1f2dafd3-52ca-43b5-b816-ff15a574232f)



# UI 





# Communication
- **Using Websocket bi-directional communication of Edge, Backend, Ui**


## 1) JSON-RPC 

### 1.1) JSON-RPC Request
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/db887315-369b-428e-96c5-96e7e2f09912)

- JSON RPC hiểu đơn giản là nó không giống dạng data để server xử lý như body của HTTP Post request mà nó sẽ dùng để trigger một function remote trên server


### 1.2) JSON-RPC Success Response
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/ff6bec11-9264-4bf1-8fa0-89f384080e91)



### 1.3) JSON-RPC Error Response
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/1d1a83f3-a3bd-4740-b11a-a2b71b79be90)


### 1.4) JSON-RPC Notification
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/27e36256-7f62-4ab4-86ce-51163fe73349)


## 2) Example JSON-RPC of UI and Backend

### 2.1) Get Edges
#### UI 
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/5aa09850-0902-4286-9a59-644de0ee05a7)
  
#### Backend
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/14cac11e-4675-45f3-af0d-689fa27b8887)

#### Example response getEdges
```js
{
    "jsonrpc": "2.0",
    "id": "4efdd058-7ece-4204-82c8-472c78d46173",
    "result": {
        "edges": [
            {
                "id": "edge0",
                "comment": "OpenEMS Edge #0",
                "producttype": "",
                "version": "2024.1.0-SNAPSHOT",
                "role": "admin",
                "isOnline": true,
                "lastmessage": "2023-12-12T04:15:00Z",
                "sumState": "OK"
            }
        ]
    }
}
```

### 2.2) Subsribe channel
>Real-time channel data may change at a high rate. Instead of requiring the consumer to constantly pull the data, the OpenEMS API provides a publish-subscribe schema that notifies the consumer about updated values. It is initiated via a JSON-RPC request

#### UI

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/f617c901-5ef7-4c5e-bc23-7ce033d9bb59)

The parameters for subscribing to channel data are:

- count: a request count value that needs to be increased on each request to avoid concurrency problems

- channels: a list of channel addresses as strings, e.g. "ess0/Soc", "ess0/ActivePower"
- Channel Address được cấu thành từ "component-Id" / "ChannelId"

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/7b076e85-a098-4f61-b637-aaa6a21541f1)

Tương tự ta cũng có Subscribe Edges

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/74e040a1-223f-4eec-b118-786c19f20624)



**To unsubscribe from channels, a new subscribeChannels request has to be sent with an empty list of channels.**

#### Flow 
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/9d56e77b-ff74-47b0-b371-5e72145ceaf9)


### 2.3) Get Edges Detail ( Edge Rpc )
![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/d25e9a01-4ec4-4e0c-a276-cce3c088f560)

**Parameter cần có:**
- edgeId: the unique ID of the Edge at the Backend
- payload: the JSON-RPC Request that should be forwarded to the Edge, e.g. getEdgeConfig or updateComponentConfig.
#### UI

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/880ccd6b-1946-485d-90ad-7f17a645d8a7)


#### Backend

![image](https://github.com/minhthuong031103/OpenEms-Docs/assets/101078033/b131b5b5-7526-41e4-a615-b9c1ea8a5f5f)

#### Example result of Edge Rpc method:
Dựa trên các channel mà ui đã subcribe thì khi data channel đó thay đổi thì sẽ send lại result
```js
{
    "jsonrpc": "2.0",
    "method": "edgeRpc",
    "params": {
        "edgeId": "edge0",
        "payload": {
            "jsonrpc": "2.0",
            "method": "currentData",
            "params": {
                "_sum/ConsumptionActivePower": 1154,
                "_sum/ConsumptionActivePowerL1": 385,
                "_sum/ConsumptionActivePowerL2": 385,
                "_sum/ConsumptionActivePowerL3": 385,
                "_sum/ConsumptionMaxActivePower": 11800,
                "_sum/EssActivePower": 1272,
                "_sum/EssActivePowerL1": 424,
                "_sum/EssActivePowerL2": 424,
                "_sum/EssActivePowerL3": 424,
                "_sum/EssMaxApparentPower": 10000,
                "_sum/EssSoc": 32,
                "_sum/GridActivePower": -118,
                "_sum/GridActivePowerL1": -39,
                "_sum/GridActivePowerL2": -39,
                "_sum/GridActivePowerL3": -39,
                "_sum/GridMaxActivePower": 1800,
                "_sum/GridMinActivePower": -300,
                "_sum/GridMode": 1,
                "_sum/ProductionAcActivePower": null,
                "_sum/ProductionActivePower": null,
                "_sum/ProductionDcActualPower": null,
                "_sum/ProductionMaxActivePower": null,
                "_sum/State": 0,
                "ess0/ActivePower": 1272,
                "ess0/Capacity": 10000,
                "ess0/Soc": 32
            }
        }
    }
}
```

