### 一、原始的Snowflake算法
![image](/uploads/976d0f8142d72d9acb0de62fd53db256/image.png)

```
0  1位，符号位，不使用
x0000000-00000000-00000000-00000000-00000000-00  41位，时间戳
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xx000000-0000  10位机器ID+进程ID 
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxx0000-00000000  12位序列号
```
缺点：无法保证唯一性

### 二、改进的Snowflake算法

//此处应该有张图

```
0  1位，符号位，不使用
x0000000-00000000-00000000-00000000-00000000-00  41位，时间戳
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xx000000-00000000-00  16位IP地址，取IPv4地址后16位，取值范围：xxx.xxx.0-255.0-255
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xx000000  6位序列号，0-63
```

#### 1、唯一性，依赖IP地址及单进程启动

#### 2、理论每秒生成64000个不同ID

#### 3、不可使用本地ip，127开头

#### 4、同一时间戳序号不大于63

```php
//rpc config
return [
    'swoole' => [
        'host' => 'swoole-rpc',
    ]
];

//rpc call
$result = \Core\Rpc\Client::getInstance()->call('swoole', ['globalId']);
echo json_encode($result); //{"code":0,"msg":"","data":"6516216470152675648"}
```

缺点：每台机器只能单进程生成ID

### 三、多机房的Snowflake算法
//此处应该有张图

```
0  1位，符号位，不使用
x0000000-00000000-00000000-00000000-00000000-00  41位，时间戳
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xx0000  4位机房ID，0-15，最多16个机房
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxx00-00000000-00  12位IP地址，取IPv4地址后12位，取值范围：xxx.xxx.0-15.0-255
xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xxxxxxxx-xx000000  6位序列号，0-63
```

缺点：IPv4地址第三段必须限制在0-15，如172.18.***15***.3