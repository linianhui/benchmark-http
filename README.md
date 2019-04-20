# 1 HTTP Benchmark 环境准备

# 1.1 准备2个HTTP服务器
```sh
docker-compose up --detach --build
```

| server port | note                         |
| ----------- | ---------------------------- |
| 60001       | http/1.1                     |
| 60002       | http/2 http2-prior-knowledge |


验证是否成功启动
```sh
curl --http1.1 http://127.0.0.1:60001

curl --http2-prior-knowledge http://127.0.0.1:60002
```

# 1.2 准备1个HTTP客户端
```sh
docker build --tag http-client ./client/
```

# 2 测试

```sh
# http/1.1
docker run -it http-client h2load -c200 -n100000 --h1 http://192.168.2.201:60001

starting benchmark...
spawning thread #0: 200 total client(s). 100000 total requests
Application protocol: http/1.1
progress: 10% done
progress: 20% done
progress: 30% done
progress: 40% done
progress: 50% done
progress: 60% done
progress: 70% done
progress: 80% done
progress: 90% done
progress: 100% done

finished in 6.47s, 15456.18 req/s, 12.42MB/s
requests: 100000 total, 100000 started, 100000 done, 100000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 100000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 80.35MB (84249000) total, 17.92MB (18795000) headers (space savings 0.00%), 58.36MB (61200000) data
                     min         max         mean         sd        +/- sd
time for request:      153us     73.38ms     12.78ms      4.51ms    72.82%
time for connect:       29us      2.79ms       168us       477us    92.50%
time to 1st byte:     8.65ms     30.75ms     19.81ms      4.82ms    67.00%
req/s           :      77.39       79.89       78.06        0.43    82.00%
```

```sh
# http2-prior-knowledge m=1
docker run -it http-client h2load -c200 -n100000 -m1 http://192.168.2.201:60002

starting benchmark...
spawning thread #0: 200 total client(s). 100000 total requests
Application protocol: h2c
progress: 10% done
progress: 20% done
progress: 30% done
progress: 40% done
progress: 50% done
progress: 60% done
progress: 70% done
progress: 80% done
progress: 90% done
progress: 100% done

finished in 9.51s, 10513.22 req/s, 7.41MB/s
requests: 100000 total, 100000 started, 100000 done, 100000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 100000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 70.49MB (73909800) total, 10.40MB (10900000) headers (space savings 38.76%), 58.36MB (61200000) data
                     min         max         mean         sd        +/- sd
time for request:      213us       1.79s     18.48ms     29.35ms    99.92%
time for connect:     3.89ms     31.13ms     14.97ms     10.46ms    46.00%
time to 1st byte:    16.89ms       1.80s    357.11ms    544.30ms    82.00%
req/s           :      52.73       55.08       54.02        0.63    67.50%
```

```sh
# http2-prior-knowledge m=10
docker run -it http-client h2load -c200 -n100000 -m10 http://192.168.2.201:60002

starting benchmark...
spawning thread #0: 200 total client(s). 100000 total requests
Application protocol: h2c
progress: 10% done
progress: 20% done
progress: 30% done
progress: 40% done
progress: 50% done
progress: 60% done
progress: 70% done
progress: 80% done
progress: 90% done
progress: 100% done

finished in 4.90s, 20428.10 req/s, 14.40MB/s
requests: 100000 total, 100000 started, 100000 done, 100000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 100000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 70.49MB (73909800) total, 10.40MB (10900000) headers (space savings 38.76%), 58.36MB (61200000) data
                     min         max         mean         sd        +/- sd
time for request:      257us       1.92s     92.82ms     83.03ms    98.00%
time for connect:     1.50ms     32.88ms     16.34ms     11.61ms    35.00%
time to 1st byte:    21.11ms       1.94s    335.67ms    516.76ms    82.00%
req/s           :     102.80      113.40      107.08        2.02    68.50%
```

```sh
# http2-prior-knowledge m=100
docker run -it http-client h2load -c200 -n100000 -m100 http://192.168.2.201:60002

starting benchmark...
spawning thread #0: 200 total client(s). 100000 total requests
Application protocol: h2c
progress: 10% done
progress: 20% done
progress: 30% done
progress: 40% done
progress: 50% done
progress: 60% done
progress: 70% done
progress: 80% done
progress: 90% done
progress: 100% done

finished in 3.55s, 28176.24 req/s, 19.86MB/s
requests: 100000 total, 100000 started, 100000 done, 100000 succeeded, 0 failed, 0 errored, 0 timeout
status codes: 100000 2xx, 0 3xx, 0 4xx, 0 5xx
traffic: 70.49MB (73909800) total, 10.40MB (10900000) headers (space savings 38.76%), 58.36MB (61200000) data
                     min         max         mean         sd        +/- sd
time for request:    40.13ms       1.32s    632.70ms    161.49ms    77.65%
time for connect:     6.27ms     61.61ms     27.21ms     10.12ms    78.00%
time to 1st byte:    62.71ms    989.11ms    518.88ms    274.03ms    58.00%
req/s           :     140.88      172.80      155.69        9.15    57.00%
```

# 术语

1. http/1.0 : http/1.0
2. http/1.1 : 基于tcp，默认支持长连接
3. h2       : 基于tls，支持多路复用.
4. h2c : 基于tcp, 明文版的h2.
5. http2-prior-knowledge : 基于tcp，客户端不经过http的协议升级协商，直接发起2的请求.

# 参考

h2load介绍： https://nghttp2.org/documentation/h2load-howto.html