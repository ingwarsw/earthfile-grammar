FROM node:13.10.1-alpine3.11

RUN npm install -g vsce
WORKDIR /earthfile-syntax-highlighting

package:
    COPY . ./
    ARG VSCODE_RELEASE_TAG
    RUN test -n "$VSCODE_RELEASE_TAG"
    RUN VERSION=${VSCODE_RELEASE_TAG#v} ; \
        sed -i 's^0.0.0^'"$VERSION"'^g' ./package.json
    RUN vsce package
    RUN VERSION=${VSCODE_RELEASE_TAG#v} ; \
        mv ./earthfile-syntax-highlighting-$VERSION.vsix ./earthfile-syntax-highlighting-$VSCODE_RELEASE_TAG.vsix
    SAVE ARTIFACT ./earthfile-syntax-highlighting-$VSCODE_RELEASE_TAG.vsix

release:
    ARG VSCODE_RELEASE_TAG
    RUN test -n "$VSCODE_RELEASE_TAG"
    COPY --build-arg VSCODE_RELEASE_TAG="$VSCODE_RELEASE_TAG" +package/earthfile-syntax-highlighting-$VSCODE_RELEASE_TAG.vsix ./
    RUN --secret VSCE_TOKEN=+secrets/earthly-technologies/vsce/token test -n "$VSCE_TOKEN"
    RUN --push \
        --secret VSCE_TOKEN=+secrets/earthly-technologies/vsce/token \
        vsce publish --pat "$VSCE_TOKEN" --packagePath ./earthfile-syntax-highlighting-$VSCODE_RELEASE_TAG.vsix
