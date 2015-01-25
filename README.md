# Cinder-EDSDK
`Cinder-EDSDK` is a [CinderBlock](http://libcinder.org/docs/welcome/CinderBlocks.html) to interact with Canon cameras. See Canon's [compatibility chart](http://www.usa.canon.com/cusa/consumer/standard_display/sdk_homepage#SDKQ7) for a list of cameras supported in their EDSDK.

*NOTE* - `Cinder-EDSDK` is configured for Cinder's [glNext branch](https://github.com/Cinder/Cinder/tree/glNext), use rev [0cfeedc4](https://github.com/pizthewiz/Cinder-EDSDK/commit/0cfeedc4aa0d3dc97c3c4208b0f7a973c188677c) for Cinder 0.8.6 support. Canon's EDSDK does not yet support `x86_64`, so Cinder must be built with an `i386` slice and applications must be built `i386` only.

### FEATURES
Image capture to memory or on-disk, camera keep-alive, simultaneous control of multiple cameras, camera connect and disconnect handling. It does *not* offer Live View support.

### EXAMPLE
```C++
void CaptureApp::setup() {
    CameraBrowser::instance()->connectAddedHandler(&CaptureApp::browserDidAddCamera, this);
    CameraBrowser::instance()->start();
}

void CaptureApp::browserDidAddCamera(CameraRef camera) {
    mCamera = camera;
    mCamera->connectFileAddedHandler(&CaptureApp::didAddFile, this);
    EdsError error = mCamera->requestOpenSession();
    if (error == EDS_ERR_OK) {
        mCamera->requestTakePicture();
    }
}

void CaptureApp::didAddFile(CameraRef camera, CameraFileRef file) {
    fs::path destinationFolderPath = expandPath(fs::path("~/Desktop/Captures"));
    camera->requestDownloadFile(file, destinationFolderPath, [this](EdsError error, fs::path outputFilePath) {
        if (error == EDS_ERR_OK) {
            console() << "downloaded image to: " << outputFilePath << endl;
        }
    });
}
```

### INTEGRATION
- Apply for EDSDK access from [Canon USA](http://www.usa.canon.com/cusa/consumer/standard_display/sdk_homepage) or [Canon Europa](https://www.didp.canon-europa.com) (serving Europe, Africa and The Middle East)
- Wait hours / days / weeks / months
- Download
- Unarchive
- Move the _EDSDK_ folder into _Cinder-EDSDK/lib_

*NOTE* - Due to a bug introduced in EDSDK v2.10, the `CameraBrowser` will only call the camera removed handler for cameras with an open session.

### GREETZ
- [Red Paper Heart](http://www.redpaperheart.com)'s [Cinder-CanonSdk](https://github.com/redpaperheart/Cinder-CanonSdk)
- [v002-Camera-Live](https://github.com/v002/v002-Camera-Live)
