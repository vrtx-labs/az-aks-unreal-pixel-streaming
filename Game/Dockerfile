# ---------------------------------------------------------------------------
# This is a sample file that will build an Unreal Engine 5 project with pixel streaming.
# It uses an Unreal "dev" container for building and then copies the packaged project
# into a "runtime-pixel-streaming".
# ---------------------------------------------------------------------------

# Perform the build in an Unreal Engine container image that includes the Engine Tools and Pixel Streaming for Linux
FROM --platform=${BUILDPLATFORM:-linux/amd64} ghcr.io/epicgames/unreal-engine:dev-5.0.0 AS build

# Copy Unreal project from source to "project" folder in container
# Assuming we are currently in the root folder of your project containing the .uproject file
COPY --chown=ue4:ue4 . /project

WORKDIR  /project

# Package the Unreal project. Adjust name of the .uproject file to match your project.
RUN /home/ue4/UnrealEngine/Engine/Build/BatchFiles/RunUAT.sh \
    BuildCookRun \
    -utf8output \
    -platform=Linux \
    -clientconfig=Shipping \
    -serverconfig=Shipping \
    -project=/project/MyDemoProject.uproject \
    -noP4 -nodebuginfo -allmaps \
    -cook -build -stage -prereqs -pak -archive \
    -archivedirectory=/project/Packaged 

# Copy the packaged files into the 'runtime-pixel-streaming' container that includes CUDA but no UE components
FROM --platform=${BUILDPLATFORM:-linux/amd64} ghcr.io/epicgames/unreal-engine:runtime-pixel-streaming
WORKDIR /home/ue4/project
COPY --from=build --chown=ue4:ue4 /project/Packaged/Linux ./


# Start pixel streaming with the desired parameters
# Parameters can be found here: https://docs.unrealengine.com/5.0/en-US/unreal-engine-pixel-streaming-reference/
# Adjust name of the .sh file to match your project.
CMD ["/bin/bash", "-c", "./MyDemoProject.sh -PixelStreamingURL=${SIGNALSERVER_URL} -RenderOffscreen -Unattended -ResX=1920 -ResY=1080 -Windowed -ForceRes -StdOut" ]
