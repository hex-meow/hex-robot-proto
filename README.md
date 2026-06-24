# hex-robot-proto

机器人公开 API 的 **protobuf 契约**(单一真相源),被控制器程序、GUI、上位机 SDK 等共享。
设计见 `hex-meow/robot-overall-design/`。

> 本仓库**暂不含机械臂**(`arm.proto`)——先做底盘最小闭环,需要时再加。

## 内容
| 文件 | 内容 |
|---|---|
| `proto/common.proto` | 版本/Header/RobotKind/OperatingMode/RobotMode/OvertakenReason |
| `proto/controller.proto` | 控制器级:ImuData/EstopState/RemoteState/ControllerInfo/Reboot |
| `proto/robot.proto` | robot 级:RobotDescription/RobotStatus/会话 RPC |
| `proto/base.proto` | 底盘:Twist/BaseCommand/Odometry/BaseLimits/BaseDescription |

package 统一为 `robot_api`。演进规则:只增不改、不复用 tag、semver(见设计文档 01)。

## 被其他仓库引用(git submodule)
在消费仓库里:
```bash
git submodule add git@github.com:hex-meow/hex-robot-proto.git proto-api
git submodule update --init --recursive
```
- **Rust**(prost):`build.rs` 里 `prost_build::compile_protos(&["proto-api/proto/common.proto", ...], &["proto-api/proto"])`。
- 更新到最新契约:`git -C proto-api pull && git add proto-api && git commit`。

> 本地开发(尚未推到 GitHub)时,消费仓库可先用相对路径引用 `../hex-robot-proto/proto`,
> 推上去后再换成上面的 submodule。

## 升级路径:buf(可选,推荐多语言时)
多语言 codegen(Rust + TS + Python)变多后,建议上 [`buf`](https://buf.build):
本仓库已带 `buf.yaml`;消费方 `buf generate`(配 `buf.gen.yaml`)即可,比 submodule 体验好、还能做 lint/breaking 检测。
