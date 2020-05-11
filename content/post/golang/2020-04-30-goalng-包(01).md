+++
title="go 包练习（一）| go viper cron cobra"
tags=["go"]
categories=["go"]
date="2020-04-28T21:00:00+08:00"

+++
## 1.读取配置文件viper
- https://github.com/spf13/viper  
```go
// 部分代码
    cfgFile := "/etc/local/go.yaml"
	_, err := os.Stat(cfgFile)
	if err != nil {
		t.Fatal(fmt.Sprintf("-_- miss : %s -_- ", cfgFile))
	}

	f, err := os.Open(cfgFile)
	if err != nil {
		log.Fatal(errors.ErrorStack(err))
	}
	defer f.Close()

	viper.SetConfigType("yaml")
	if err = viper.ReadConfig(f); err != nil {
		log.Fatal(errors.ErrorStack(err))
	}
    viper.Get("name")
```

## 2.定时任务cron
```
github.com/gogits/cron
```

## 3.子命令cobra