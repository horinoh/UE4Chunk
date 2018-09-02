# UE4Chunk

# ���������
* HTTPChunkInstaller �v���O�C����L���ɂ��� Enable HTTPChunkInstaller Plugin

![�摜](EnablePlugin.png)

* ChunkBuild.cs
~~~
ModuleNames = "HTTPChunkInstaller"
~~~

## [�e�X�g�p�T�[�o���� Prepare test server](https://github.com/horinoh/UE4Analytics/tree/master/Document/Server)

# ���� Memo

## �A�Z�b�g�ɐݒ肳�ꂽ ChunkID �Ɋ�Â��ăp�b�P�[�W (.pak) �ɕ��������
* ChunkID ������ PrimaryAsset �Ƃ��ĊǗ��A�܂��͂��� SecondaryAsset �Ƃ��ĊǗ������

## PrimaryAsset
* PrimaryAssetID
* ChunkID

## �A�Z�b�g�� PrimaryAsset�ASecondaryAsset �Ƃ��ĊǗ�����
* �����̐ݒ肩�� ChunkID ���w�肳���ꍇ�A�A�Z�b�g�͗����� ChunkID �Ɋ܂܂�邱�ƂɂȂ�
    * �e Chunk �Ɋ܂܂��A�Z�b�g�́A�q Chunk �ɂ͊܂߂Ȃ� DefaultEngine.ini �ݒ�
    ~~~
    [/Script/UnrealEd.ChunkDependencyInfo]
    +DependencyArray=(ChunkID=100,ParentChunkID=10)
    ~~~
### AssetManager
### PrimaryAssetLabel

## Chunk �̊m�F
* AssetAudit
    * ReferenceViewer
    * SizeMap

## �p�b�P�[�W
* ProjectSettings �� ProjectLauncher �� Chunk �ݒ������
* CloudDir �֐������ꂽ���̂��T�[�o�ɃA�b�v����Ηǂ�
    * Manifest �̓o�[�W�������������A�ă_�E�����[�h���邩�ǂ����̔��f�Ɏg�p�����

## �_�E�����[�h
* Chunk �p�b�P�[�W���T�[�o�ɃA�b�v
* �v���b�g�t�H�[������ ini(Config/Windows/WindowsEngine.ini) �ɃT�[�o����ǋL
~~~
[HTTPChunkInstall]
TitleFileSource=Http
CloudProtocol=Http
CloudDomain=192.168.XXX.XXX
CloudDirectory=CloudDir

[HTTPOnlineTitleFile]
BaseUrl="192.168.XXX.XXX"
EnumerateFileUrl=CloudDir

[StreamingInstall]
DefaultProviderName=HTTPChunkInstaller
~~~
* HttpChunkInstaller �v���O�C����L���ɂ���
* Build.cs �� ModuleNames �� "HTTPChunkInstaller" ��ǉ�

## ���[�h���ɕK�v�ȃA�Z�b�g�������_�E�����[�h (�A�Z�b�g�̃f�[�^���������ɏ悹��Ƃ���܂�)
* AssetManager �� ShouldAcquireMissingChunksOnLoad �Ƀ`�F�b�N
~~~
UAssetManager::AsyncLoadPrimaryAssetXXX
UAssetManager::UnloadPrimaryAssetXXX
~~~
* PrimaryAsset ��**����**�Q�Ƃ��Ă��� SecoundaryAsset ���������[�h�����

### AssetBundle
* ��p��**�^�O��**������ SecondaryAsset
    * AsyncLoadPrimaryAsssetXXX �̈�����**�^�O��**��n���ăR�[������
~~~
UPROPERTY(..., meta=(AssetBundles="Explicit"))
TArray<TSoftObjectPtr<UObject>> ExplicitAssets;
UPROPERTY(..., meta=(AssetBundles="Directory"))
...
UPROPERTY(..., meta=(AssetBundles="Collection"))
...
~~~

### �A�Z�b�g�̎擾
* UAssetManager::AsyncLoadPrimaryAssetXXX �̕Ԃ�l
* UAssetManager::GetAssetBundleEntry

#### AssetRegistry ����A�Z�b�g����������
* ���O�ł��̂ő�ρA��{�� PrimaryAsset �������悤�Ȑ݌v�ɂ����ق����ǂ�
* �N���X���A�p�X���Ńt�B���^�����O�����\
* FAssetData::GetAsset ����A�Z�b�g���擾
    * ���_�E�����[�h�Ȃ� nullptr ���Ԃ�̂ŁAUAssetManager::LoadAssetList �Ń��[�h����

## ChunkID �̐ݒ������������ɂ�
* UAssetManager::UpdateManagementDatabase �Ɏ����ݒ菈����ǉ�����

<!--
�A�Z�b�g�ɐݒ肳�ꂽChunkID�Ɋ�Â��ăQ�[���f�[�^��.pak�ɕ���
�S�A�Z�b�g�� PrimaryAsset(PA) �� SecondaryAsset(SA) �ɕ��ނ� AssetManager(AM) �ŊǗ�����
    PA �� ChunkID ������ AM �ŊǗ������
    SA �� PA �ɕR�Â� (PA ������or�֐ߓI�� SA ���Q�Ƃ��Ă���)
    �����̕��@�� ChunkID ���ݒ肳��Ă���ꍇ�A�ݒ肳��Ă���S�Ă� ChunkID �Ɋ܂܂�邱�ƂɂȂ�
    
    Chunk �Ԃ̃A�Z�b�g������� : �e�q�֌W (�eChunk�Ɋ܂܂��A�Z�b�g�́A�qChunk�ɂ͊܂܂�Ȃ�)
    DefaultEngine.ini
    [/Script/UnrealEd.ChunkDependencyInfo]
    +DependencyArray=(ChunkID=100,ParentChunkID=11)

�p�b�P�[�W�쐬������ACloudDir �ȉ���z�M�p�T�[�o�ɃA�b�v����Ηǂ�
    ini �ɃT�[�o�ݒ��ǉ�����
    HttpChunkInstaller �v���O�C����L��������
    AM �� Should Acquire Missing Chunk on Load ��L���ɂ���
        �ꊇ DL : HttpChunkInstaller �̃��N�G�X�g�֐��Ăяo��
        ���� DL : AM �̃��[�h�֐��ł��̎��K�v�Ȃ��̂� DL
            �ԐڎQ�� SA �͖����I�Ƀ��[�h�������ĂԕK�v���� -> AB �ɂ��܂��傤

AssetBundle(AB)
    �^�O�����ݒ肳�ꂽ�ԐڎQ�� SA
    PA �̃��[�h���ɁA���[�h������ AB �̃^�O����n��

    UPROPERTY(..., meta=(AssetBundles="Explicit"))
    TArray<TSoftObjectPtr<UObject>> ExplicitAssets;

Chunk�����蓖�Ă�
    AssetManager(AM)
        PA�ASA���Ǘ�
    PrimaryAssetLabel(PAL)
        ChunkID�ݒ�ɓ��������A�Z�b�g
Chunk��\��
    AssetAudio(AA)
-->