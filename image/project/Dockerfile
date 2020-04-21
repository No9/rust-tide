FROM rust:1.42-buster as builder

WORKDIR "/project/user-app"

COPY * /project/user-app/

# get user application dependencies
RUN cargo fetch 

#copy user code
COPY user-app/src /project/user-app/

# build for release
RUN cargo build --release \
 && echo "#!/bin/bash" > run.sh \
 && bin=$(find ./target/release -maxdepth 1 -perm -111 -type f| head -n 1) \
 && echo ./${bin##*/} >> run.sh \
 && chmod 755 run.sh

FROM rust:1.42-slim-buster

RUN useradd rust

WORKDIR "/project/user-app"

# get files and built binary from previous image
COPY --from=builder /project/user-app/run.sh /project/user-app/Cargo.toml /project/user-app/target/release/ ./

USER rust

ENV PORT 8000

EXPOSE 8000

CMD ["./run.sh"]